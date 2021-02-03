# Cheatsheet - xUnit Assertions

```csharp
using System;
using System.Collections.Generic;
using Xunit;
using Moq;

namespace CloudAcademy.Xunit.Cheatsheet.Tests
{
    public class ExampleTests : 
        IClassFixture<ExampleTests.TestSetUpClass>, // setup via injected class
        IDisposable // tearDown via Dispose()
    {
 
        public ExampleTests()
        {
            // TestFixture Setup code here
            Console.WriteLine("ExampleTests() constructor called");
        }

        public void SetFixture(TestSetUpClass setupClass)
        {
            // Setup via injected class
            setupClass.AnyFunction();
        }

        public void Dispose()
        {
            // Teardown
            Console.WriteLine("ExampleTests.Dispose() called...");
        }        

        /// <summary>
        /// Basic example.
        /// </summary>
        [Fact]
        public void BasicExample()
        {
            Assert.True(true);
        }

        /// <summary>
        /// Multiple asserts (common)
        /// </summary>
        [Fact]
        public void AssertionExamples()
        {
            Assert.Equal(1, 1);
            Assert.Equal("CloudAcademy", "CloudAcademy");
            Assert.Equal(1.95, 1.99, 1);
            Assert.Equal(new List<String> { "cloud", "academy" }, 
                         new List<String> { "CLOUD", "ACADEMY" }, 
                         StringComparer.CurrentCultureIgnoreCase);
 
            Assert.NotEqual("Cloud", "Academy");
            Assert.NotEqual(1, 2);
            Assert.NotEqual(new List<String> { "A", "B" }, 
                            new List<String> { "1", "2" });
 
            Assert.Contains("cloud", 
                            "CloudAcademy", 
                            StringComparison.CurrentCultureIgnoreCase);

            Assert.Contains("cloud", 
                            new List<String> { "Cloud", "Academy" }, 
                            StringComparer.CurrentCultureIgnoreCase);

            Assert.Empty(new List<String>());
            Assert.NotEmpty(new List<String> { "Cloud", "Academy" });
 
            Assert.True(true);
            Assert.False(false);
            Assert.Null(null);
            Assert.NotNull(new String("blah"));
 
            Assert.InRange(1, 0, 10);
            Assert.NotInRange(-1, 0, 10);
 
            Assert.IsType(Type.GetType("System.Int32"), 1);
            Assert.IsNotType(Type.GetType("System.Double"), 1);
  
            Assert.ThrowsAsync<Exception>(() => { throw new Exception(); });
            Assert.ThrowsAsync<ArgumentException>(() => { throw new ArgumentException(); });

            var obj1 = new object();
            var obj2 = new object();
 
            Assert.Same(obj1, obj1);
            Assert.NotSame(obj1, obj2);
        }        
 
        /// <summary>
        /// Parameterized test
        /// </summary>
        /// <param name="number"></param>
        /// <param name="expectedResult"></param>
        [Theory]
        [InlineData(1, true)]
        [InlineData(2, true)]
        [InlineData(3, false)]
        public void InlineDataExample(int number, bool expectedResult)
        {
            Assert.Equal(number < 3, expectedResult);
        }
 
        /// <summary>
        /// Properties returning IEnumerable can be used instead of InLineData.
        /// </summary>
        public static IEnumerable<object[]> SamplePropertyDataProperty
        {
            get
            {
                yield return new object[] { 1, true };
                yield return new object[] { 2, true };
                yield return new object[] { 3, false };
            }
        }
 
        /// <summary>
        /// PropertyData allow multiple calls of a test function with parameters defined in a property usign yield
        /// </summary>
        /// <param name="number">Test data</param>
        /// <param name="expectedResult">Expected Result</param>
        [Theory]
        [MemberData(nameof(SamplePropertyDataProperty))]
        public void PropertyDataExample(int number, bool expectedResult)
        {
            Assert.Equal(number < 3, expectedResult);
        }

        [Fact]
        /// <summary>
        /// Mocking example
        /// </summary>
        public void MoqExample()
        {
            var mock = new Mock<IMockTarget>();

            // Configure the mock to do something
            mock.SetupGet(x => x.PropertyToMock).Returns("CloudAcademy");

            // Demonstrate that the configuration works
            Assert.Equal("CloudAcademy", mock.Object.PropertyToMock);

            // Verify that the mock was invoked
            mock.VerifyGet(x => x.PropertyToMock);
 
        }
  
        # region Internal Classes
        public class TestSetUpClass
        {
            public void AnyFunction()
            {
                Console.WriteLine("TestSetUpClass.AnyFunction()");
            }
        }
        #endregion

        # region Internal Interfaces
        public interface IMockTarget
        {
            // Property declaration:
            string PropertyToMock
            {
                get;
                set;
            }            
        }
        #endregion
    }
}
```