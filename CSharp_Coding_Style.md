# C# Coding Style

For non code files (xml, etc), our current best guidance is consistency. When editing files, keep new code and changes consistent with the style in the files. For new files, it should conform to the style for that component. If there is a completely new component, anything that is reasonably broadly accepted is fine.

The general rule we follow is "use Visual Studio defaults" (with the exception of private fields).

1. We use [Allman style](http://en.wikipedia.org/wiki/Indent_style#Allman_style) braces, where each brace begins on a new line. Braces are always preferred over a single line statement block. If a block is used, the block must be properly indented on its own line and must not be nested in other statement blocks that use braces (See [dotnet/corefx issue 381](https://github.com/dotnet/corefx/issues/381) for examples). One exception is that a `using` statement is permitted to be nested within another `using` statement by starting on the following line at the same indentation level, even if the nested `using` contains a controlled block.
2. We use four spaces of indentation (no tabs).
3. We use `_camelCase` for private fields and use `readonly` where possible. Prefix private instance fields with `_`. When used on static fields. Public fields should be used sparingly and should use PascalCasing with no prefix when used.
4. We avoid `this.` unless absolutely necessary.
5. We always specify the visibility, even if it's the default (e.g.
   `private string _foo` not `string _foo`).
6. Namespace imports should be specified at the top of the file, *outside* of
   `namespace` declarations, and should be sorted alphabetically.
7. Avoid more than one empty line at any time. For example, do not have two
   blank lines between members of a type.
8. Avoid spurious free spaces.
   For example avoid `if (someVar == 0)...`, where the dots mark the spurious free spaces.
   Consider enabling "View White Space (Ctrl+E, S)" if using Visual Studio to aid detection.
9. If a file happens to differ in style from these guidelines (e.g. private members are named `m_member`
   rather than `_member`), the existing style in that file takes precedence.
10. We prefer `var`, but if it's not obvious what the variable type is (e.g. `var stream = OpenStandardInput()`, not `var stream = new FileStream(...)`), then use the type name instead of `var`.
11. We use language keywords instead of BCL types (e.g. `int, string, float` instead of `Int32, String, Single`, etc) for both type references as well as method calls (e.g. `int.Parse` instead of `Int32.Parse`).
12. We use PascalCasing to name all our constant local variables and fields.
13. We use `nameof(...)` instead of `"..."` whenever possible and relevant.
14. Fields should be specified at the top within type declarations.
15. When including non-ASCII characters in the source code use Unicode escape sequences (\uXXXX) instead of literal characters. Literal non-ASCII characters occasionally get garbled by a tool or editor.
16. When using labels (for goto), indent the label one less than the current indentation.


### Example File:

``ObservableLinkedListT.cs:``

```C#
using System;
using System.Collections;
using System.Collections.Generic;
using System.Collections.Specialized;
using System.ComponentModel;
using System.Diagnostics;
using Microsoft.Win32;

namespace System.Collections.Generic
{
    public partial class ObservableLinkedList<T> : INotifyCollectionChanged, INotifyPropertyChanged
    {
        private ObservableLinkedListNode<T> _head;
        private int _count;

        public ObservableLinkedList(IEnumerable<T> items)
        {
            if (items == null)
                throw new ArgumentNullException(nameof(items));

            foreach (T item in items)
            {
                AddLast(item);
            }
        }

        public event NotifyCollectionChangedEventHandler CollectionChanged;

        public int Count
        {
            get { return _count; }
        }

        public ObservableLinkedListNode AddLast(T value)
        {
            var newNode = new LinkedListNode<T>(this, value);

            InsertNodeBefore(_head, node);
        }

        protected virtual void OnCollectionChanged(NotifyCollectionChangedEventArgs e)
        {
            NotifyCollectionChangedEventHandler handler = CollectionChanged;
            if (handler != null)
            {
                handler(this, e);
            }
        }

        private void InsertNodeBefore(LinkedListNode<T> node, LinkedListNode<T> newNode)
        {
           ...
        }

        ...
    }
}
```

``ObservableLinkedListNodeT.cs:``

```C#
using System;

namespace System.Collections.Generics
{
    partial class ObservableLinkedList<T>
    {
        public class ObservableLinkedListNode
        {
            private readonly ObservableLinkedList<T> _parent;
            private readonly T _value;

            internal ObservableLinkedListNode(ObservableLinkedList<T> parent, T value)
            {
                Debug.Assert(parent != null);

                _parent = parent;
                _value = value;
            }

            public T Value
            {
               get { return _value; }
            }
        }

        ...
    }
}
```

# Testing

1. The test method's name should adere to this style: MethodName_StateUnderTest_ExpectedBehavior
1. The test method's body should adere to this style: Arrange, Act, Assert. The arrange, act, assert comments can be ommitted, but are present in the example below for illustration.
1. Use NUnit for all unit tests, and Moq where necessary
1. Particularly in our Phnx work, any additional logic or changes you make should have automated tests that validate their logic. Avoid writing tests that test _how_ your code works (such as calling other methods), but assert that the code produces the result (or exception) you expect. In non-phnx work, it is crucial that you test fragile or complex logic (such as logically complex services), but it is not as important to test for edge cases that are not generally expected. 

For example
```cs
[Test]
public void NewPerson_WithAValidEmail_AssignsTheEmailForTheNewUser()
{
   // Arrange
   string email = "someone@example.com";
   
   // Act
   Person newPerson = new Person(email);
   
   // Assert
   Assert.AreEqual(email, newPerson.Email);
}
```
