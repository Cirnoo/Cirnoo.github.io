---
layout: post
title:  "C#动态编译"
date:   2019-05-25 
categories: 实习 C#
tags: unity
excerpt: 在unity中使用C#动态编译
---

* content
{:toc}

在做UnityEditor动态生成C#文件并挂到Object上的任务时，由于新生成的代码没有被编译，导致使用`GetType` 获取不到类型，无法将新生成的C#作为Component挂到对象上面，想到可不可以对代码进行动态编译，获取到了类型再将代码挂到对象上呢。

首先，结论肯定是不可以，虽然通过dynamic compile的代码能够被执行，而且也能正确获取到类型，但是由于被编译的类和新生成的文件本质上是不在一个程序集里的，而且因为Unity的Serialize机制，Component都是根据Asset的ID序列化进对象，动态编译生成的Type虽然可以继承自MonoBehavior，可以被作为Component添加到Object中，但这本身是无用的，因为在运行的时候Unity并不能通过Id找到Component。

下面简单记录下C#中如何使用动态编译

#### C#动态编译

``` cs
using Microsoft.CSharp;
using System;
using System.CodeDom.Compiler;
using System.Reflection;
using System.Text;

namespace ConsoleApp1
{
	class Program
	{
		static void Main(string[] args)
		{
			RuntimeCompileTest.Test();
		}

		public class RuntimeCompileTest
		{
			public static void Test()
			{
				var assembly = Compile(@"
		using System;
        public class RuntimeCompiled
        {
            public static void Start()
            {
                Console.WriteLine(""test"");
			}
		}");
				var runtimeType = assembly.GetType("RuntimeCompiled");
				runtimeType.GetMethod("Start").Invoke(null,null);
			}

			public static Assembly Compile(string source)
			{
				// Replace this Compiler.CSharpCodeProvider wth aeroson's version
				// if you're targeting non-Windows platforms:
				var provider = new CSharpCodeProvider();
				var param = new CompilerParameters();
				// Add ALL of the assembly references
				foreach (var assembly in AppDomain.CurrentDomain.GetAssemblies())
				{
					param.ReferencedAssemblies.Add(assembly.Location);
				}

				// Or, uncomment just the assemblies you need...

				// System namespace for common types like collections.
				//param.ReferencedAssemblies.Add("System.dll");

				// This contains methods from the Unity namespaces:
				//param.ReferencedAssemblies.Add("UnityEngines.dll");

				// This assembly contains runtime C# code from your Assets folders:
				// (If you're using editor scripts, they may be in another assembly)
				//param.ReferencedAssemblies.Add("CSharp.dll");


				// Generate a dll in memory
				param.GenerateExecutable = false;
				param.GenerateInMemory = true;

				// Compile the source
				var result = provider.CompileAssemblyFromSource(param, source);

				if (result.Errors.Count > 0)
				{
					var msg = new StringBuilder();
					foreach (CompilerError error in result.Errors)
					{
						msg.AppendFormat("Error ({0}): {1}\n",
							error.ErrorNumber, error.ErrorText);
					}
					throw new Exception(msg.ToString());
				}

				// Return the assembly
				return result.CompiledAssembly;
			}
		}
	}

}

```

可以看到，C#构造了一个程序集，新的代码在运行过程中被编译了，但是现在新编译的代码并不能引用外部的类，需要加入程序集才可以，有个比较简单的方法获取程序集，使用`typeof(xxx).Assembly`，当需要引用的外部类比较多的时候可用`GetExecutingAssembly()`