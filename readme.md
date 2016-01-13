# AutoFactory #

**LightInject.AutoFactory** enables automatic implementation of factory interfaces.

## Installing ##

**LightInject.AutoFactory** provides two distribution models via NuGet

### Binary ###

<div class="nuget-badge" >
   <p>
         <code>PM&gt; Install-Package LightInject.AutoFactory </code>
   </p>
</div>

This adds a reference to the **LightInject.AutoFactory.dll** in the target project.

### Source ###

<div class="nuget-badge" >
   <p>
         <code>PM&gt; Install-Package LightInject.AutoFactory.Source </code>
   </p>
</div>

This will install a single file, **LightInject.AutoFactory.cs** in the target project.


## Factory ##

The following interface represents a factory class used to resolve **IFoo** instances. 

	public interface IFoo {}	
	
	public class Foo : IFoo {}	

	public interface IFooFactory
	{
		IFoo GetFoo();
	} 

Instead of having to manually implement the **IFooFactory** interface, we can just register the factory and resolve the instance through a factory that has been automatically implemented.

	container.Register<IFoo, Foo>();	
	container.RegisterAutoFactory<IFooFactory>(); 
	var factory = container.GetInstance<IFooFactory>();
	var foo = factory.GetFoo();

## Parameters ##
	
	public interface IFoo {}	

	public class Foo : IFoo 
	{
		public Foo(int value) {}		
	}

	public interface IFooFactory
	{
		IFoo GetFoo(int value);
	} 

Runtime arguments can now be passed to the factory method.

	container.Register<int, IFoo>((factory, value) => new Foo(value));	
	container.RegisterAutoFactory<IFooFactory>();
	var factory = container.GetInstance<IFooFactory>();
	var foo = factory.GetFoo(42);


## Named Services ##

	public interface IFoo {}	
	
	public class Foo : IFoo {}	

	public class AnotherFoo : IFoo {}	

	public interface IFooFactory
	{
		IFoo GetFoo();
		IFoo GetAnotherFoo();
	} 	
	
The name of the factory method is used to identify named services.

	container.Register<IFoo, Foo>();	
	container.Register<IFoo, Foo>("AnotherFoo");	
	container.RegisterAutoFactory<IFooFactory>();
	var factory = container.GetInstance<IFooFactory>();
	var foo = factory.GetFoo();
	var anotherFoo = factory.GetAnotherFoo(); 
	     	 
## Open Generics ##

	public interface IFoo<T> {}	
	
	public class Foo<T> : IFoo<T> {}	
	
	public interface IFooFactory
	{
		IFoo<T> GetFoo<T>();	
	}

The generic type arguments from the factory method are used to create the service instance.

	container.Register(typeof(IFoo<>), typeof(Foo<>));
	container.RegisterAutoFactory<IFooFactory>();
	var factory = container.GetInstance<IFooFactory>();
	var foo = factory.GetFoo<int>();