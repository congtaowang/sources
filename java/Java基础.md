## Java基础
>
**学好Android从学好Java开始**

<br>
###函数参数列表中的final类型
>
如果函数中出现匿名内部类而且匿名内部类中又使用到了函数的参数，则必须使用final类型进行修饰，否则编译错误（视平台而定，即使编码中没有声明为final类型，编译器也会加上）~

```java
public class Parameters {

    static interface Callback {
    	void call(Parameter p);
    }
	
	static class Parameter {
	
		public String arg1;
		public String arg2;
		public String arg3;
		public String arg4;
		public InnerParameter ip = new InnerParameter();

		public Parameter() {
		}

		@Override
		public String toString() {
			return "Parameter [arg1=" + arg1 + ", arg2=" + arg2 + ", arg3="
					+ arg3 + ", arg4=" + arg4 + ", ip=" + ip.toString() + "]";
		}
	}
	
	static class InnerParameter {
	
		public String innerArg1 = "InnerParameter1";
		public String innerArg2 = "InnerParameter2";

		@Override
		public String toString() {
			return "InnerParameter [innerArg1=" + innerArg1 + ", innerArg2="
					+ innerArg2 + "]";
		}
	}
	
static void invoke(final Parameter p, Callback callback) {
		new Thread(new Runnable() {
			public void run() {
				try {
					Thread.sleep(3000);
					callback.call(p);
				} catch (Exception e) {
					e.printStackTrace();
				}
			}
		}).start();
	}
	
	public static void main(String[] args) {
		Callback c = new Callback() {
			@Override
			public void call(Parameter p) {
				System.out.println(p.toString());
			}
		};
		Parameter p = new Parameter();
		p.arg1 = "ARGUMENTER1";
		p.arg2 = "ARGUMENTER2";
		p.arg3 = "ARGUMENTER3";
		p.arg4 = "ARGUMENTER4";
		invoke(p, c);
		new Thread(new Runnable() {
			public void run() {
				try {
					Thread.sleep(1000);
					InnerParameter ip = new InnerParameter();
					ip.innerArg1 = "NewInnerParameter1";
					ip.innerArg2 = "NewInnerParameter2";
					p.ip = ip;
				} catch (Exception e) {

				}
			}
		}).start();
	}
}
```

###运行结果

```
Parameter [arg1=ARGUMENTER1, arg2=ARGUMENTER2, arg3=ARGUMENTER3, arg4=ARGUMENTER4, ip=InnerParameter [innerArg1=NewInnerParameter1, innerArg2=NewInnerParameter2]]
```
上述结果表明，即使被调用函数的参数已经声明为final类型，但是参数内容还是可变的！！


一般情况下，Java中的引用类型数据，在传递进入函数时，会生成传入对象的一个拷贝对象，该对象与原对象指向同一个内存地址，如果在函数中对拷贝对象进行了**赋值修改**，原对象不会改变。但是，如果拷贝对象进行了属性修改，则原对象也会发生改变（函数外部原对象修改属性，则拷贝对象同样会改变）。

***

有时候我就在想，在**Android**开发中，如果一个网络请求回调方法内需要使用到方法请求的参数(**What the fuck demand**)，而且该参数没有在类内定义（为什么不定义，你是猪啊），那么在使用之前该参数被修改了，怎么办？

上述胡思乱想正如代码所展示的意思，如果我需要用到旧的参数，但是在使用之时参数已经被改变！

```java
public class Parameters {

	public static void main(String[] args) {
		Callback c = new Callback() {
			@Override
			public void call(Parameter p) {
				System.out.println(p.toString());
			}
		};
		Parameter p = new Parameter();
		p.arg1 = "ARGUMENTER1";
		p.arg2 = "ARGUMENTER2";
		p.arg3 = "ARGUMENTER3";
		p.arg4 = "ARGUMENTER4";
		invoke(p, c);
		new Thread(new Runnable() {
			public void run() {
				try {
					Thread.sleep(1000);
					InnerParameter ip = new InnerParameter();
					ip.innerArg1 = "NewInnerParameter1";
					ip.innerArg2 = "NewInnerParameter2";
					p.ip = ip;
				} catch (Exception e) {

				}
			}
		});
	}

	static interface Callback {
		void call(Parameter p);
	}

	static class Parameter implements Cloneable {
		public String arg1;
		public String arg2;
		public String arg3;
		public String arg4;
		public InnerParameter ip = new InnerParameter();

		public Parameter() {
		}

		@Override
		public String toString() {
			return "Parameter [arg1=" + arg1 + ", arg2=" + arg2 + ", arg3="
					+ arg3 + ", arg4=" + arg4 + ", ip=" + ip.toString() + "]";
		}

		@Override
		public Parameter clone() throws CloneNotSupportedException {
			Parameter p = (Parameter) super.clone();
			p.arg1 = arg1;
			p.arg2 = arg2;
			p.arg3 = arg3;
			p.arg4 = arg4;
			p.ip = ip.clone();
			return p;
		}

	}

	static class InnerParameter implements Cloneable {
		public String innerArg1 = "InnerParameter1";
		public String innerArg2 = "InnerParameter2";

		@Override
		public String toString() {
			return "InnerParameter [innerArg1=" + innerArg1 + ", innerArg2="
					+ innerArg2 + "]";
		}

		@Override
		public InnerParameter clone() throws CloneNotSupportedException {
			InnerParameter p = (InnerParameter) super.clone();
			p.innerArg1 = innerArg1;
			p.innerArg2 = innerArg1;
			return p;
		}

	}

	static void invoke(Parameter p, Callback callback) {
		new Thread(new Runnable() {
			public void run() {
				try {
					Parameter newP = p.clone();
					Thread.sleep(3000);
					callback.call(newP);
				} catch (Exception e) {
					e.printStackTrace();
				}
			}
		}).start();
	}

}
```

###深拷贝可解决上述问题
在可能出现问题的场景中，使用**深拷贝**首先把旧的数据给备份下来，爸爸妈妈就不用担心我的**参数**被改变了。




