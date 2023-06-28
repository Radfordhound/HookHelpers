# HookHelpers

Simple C++11 library that helps make hooking easier.

This library [is in the public domain](LICENSE.txt); feel free to copy-paste it and do whatever you want to with it.

## Examples

Simple hooking:

```cpp
// A class named Foo - let's say this class is from a game we want
// to mod, and the code for the sum() member function is in the game's
// executable at address 0x11223344.

namespace app
{
class Foo
{
public:
    int a;
    int b;
    int c;

    int sum();
};
}

// The following code creates a hook of the member function at address
// 0x11223344 (the address of the app::Foo::sum function from our game).

// The macro is to be used like this:
// MEMBER_HOOK(address, objectType, hookName, returnType, arguments...)

// address:     The address of the function you want to hook.
//              Does not account for ASLR, etc - you have to do that manually,
//              e.g. like this: (ModuleAddress + ((address) - BaseAddress))
//
// objectType:  The type of object this member function is a member of.
//
// hookName:    The name of your hook. This can be anything as long as it's
//              a valid C++ class name.
//
// returnType:  The return type of the function. Use void for non-returning functions.
//
// arguments:   The arguments of the function. This value is optional if
//              the function has no arguments.

MEMBER_HOOK(0x11223344, app::Foo,
    MyHook, int)
{
    // Returns the value of this->a + 200.
    return (a + 200);

    // If you want to call the original (non-modified) function
    // from the game, use CallOriginal(), like so:

    // int result = CallOriginal();
    // TODO: Do whatever else you want.
    // return result;
}

// You can also use CONST_MEMBER_HOOK for hooking const member functions.

// This function, when called, will install MyHook into the game.
// From then on, whenever the game calls its copy of app::Foo::sum(),
// MyHook will be called instead!

void InstallHooks()
{
    InstallHook<MyHook>();
}
```
