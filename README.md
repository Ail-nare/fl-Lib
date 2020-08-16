# fl-Lib
This lib add usefull tool to play around with template, constexpr, from C++11 to C++17(and more)

Honorable mention:
 - **fl\:\:true_type\\false_type**
 - **fl\:\:Operator\:\:Has\:\:${Any operator name}**: return fl\:\:true_type if the class 'T' has the asked operator with 'Ts...' as parameter else fl\:\:false_type
 - **fl\:\:select**
 - **fl\:\:Attribute**
 - **fl\:\:variadic\:\:get_args\<size_t\>(Ts&&...)**
 

## Table of Contents
1. [fl\:\:none](#fl\:\:none)
2. [fl\:\:undefined](#fl\:\:undefined)
3. [fl\:\:redirect](tfl\:\:redirect)
   1. [fl\:\:redirect_type](#fl\:\:redirect_type)
   2. [fl\:\:redirect_value](#fl\:\:redirect_value)
4. [fl\:\:true_type\\false_type](#fl\:\:true_type\\false_type)
   1. [return_value](#fl\:\:true_type\\false_type\:\:return_value)
   2. [func](#fl\:\:true_type\\false_type\:\:func)
   3. [func_else](#fl\:\:true_type\\false_type\:\:func_else)
   3. [conditional](#fl\:\:true_type\\false_type\:\:conditional)
   3. [enable_if](#fl\:\:true_type\\false_type\:\:enable_if)
   3. [And Or Xor Not](#fl\:\:true_type\\false_type\:\:AndOrXorNot)
5. [fl\:\:Bool](#fl\:\:Bool)
6. [namespace fl\:\:is](#fl\:\:is)

---

<a name="fl\:\:none"></a>
### `fl::none`
**fl\:\:none** is used as default template (instead of void).

    Ex: template <typename T=fl::none>


<a name="fl\:\:undefined"></a>
### `fl::undefined`
**fl\:\:undefined** is used to represents the absence of a type or a class with nothing.
It also may be use as trash class to inherit if a condition fails. 

    class fl::undefined {};
    
    // Here if the bool b is true, myClass inherit from std::string else it wont inherite anything from fl::undefined
    Ex: template<bool b> class myClass : std::conditional<b, std::string, fl::undefined>\:\:type


<a name="fl\:\:redirect"></a>
### `fl::redirect`
**fl\:\:redirect\<T\>** redirect the type T into \:\:type

    template <typename T> struct redirect { typedef T type; };
    

<a name="fl\:\:redirect_type"></a>
### `fl::redirect_type`
**fl\:\:redirect_type\<T\>** redirect the type T\:\:type into \:\:type

    template <typename T> struct redirect_type { typedef T::type type; };


<a name="fl\:\:redirect"></a>   
### `fl::redirect_value`
**fl\:\:redirect_value\<T\>** redirect the value T\:\:value into \:\:value

    template <typename T> struct redirect_value {
        static constexpr auto value = T::value;
        typedef decltype(T::value) value_type;
    };


<a name="fl\:\:true_type\\false_type"></a>
### `fl::true_type\false_type`
fl\:\:true_type\\false_type same as std\:\:true_type\\false_type but with extra tool build in the class
<br><br/> Extra tools:
<a name="fl\:\:true_type\\false_type\:\:return_value"></a>
###### - return_value

    // return 'T1& v1' if true_type else 'T2& v2'
    fl::(true_type\false_type)::return_value(T1&& v1, T2&& v2)

      fl::true_type: template <typename T1, typename T2> constexpr static T1 &return_value(T1&& v, T2&&) { return v; }
      fl::false_type: template <typename T1, typename T2> constexpr static T2 &return_value(T1&&, T2&& v) { return v; }
---
<a name="fl\:\:true_type\\false_type\:\:func"></a>
###### - func

    // return function(args...) if true_type else do nothing
    fl::(true_type\false_type)::func(F&& function, Ts&&... args)

      fl::true_type: template <typename F, typename ...Ts> constexpr static auto func(F&& function, Ts&&... args) -> decltype(function(args...)) { return function(args...); }
      fl::false_type: template <typename ...Ts> constexpr static void func(Ts&&...) {}
---
<a name="fl\:\:true_type\\false_type\:\:func_else"></a>
###### - func_else

    // return function1(args...) if true_type else function2(args...)
    fl::(true_type\false_type)::func_else(F1&& function1, F2&& function2, Ts&&... args)

      fl::true_type: template <typename F, typename N, typename ...Ts> constexpr static auto func_else(F&& function, N&&, Ts&&... args) -> decltype(function(args...)) { return function(args...); }
      fl::false_type: template <typename N, typename F, typename ...Ts> constexpr static auto func_else(N&&, F&& function, Ts&&... args) -> decltype(function(args...)) { return function(args...); }
---
<a name="fl\:\:true_type\\false_type\:\:conditional"></a>
###### - conditional

    // same as std::condictional<fl::(true_type\\false_type)::value, T1, T2>
    fl::(true_type\false_type)::conditional<T1, T2>
    
      fl::true_type: template <typename T, typename> struct conditional : redirect<T> {};
      fl::false_type: template <typename, typename T> struct conditional : redirect<T> {};
      using template <typename T1, typename T2> using conditional_t = typename conditional<T1, T2>::type;
---
<a name="fl\:\:true_type\\false_type\:\:enable_if"></a>
###### - enable_if

    // same as std::enable_if<fl::(true_type\\false_type)::value, T=void>
    fl::(true_type\false_type)::enable_if<T=void>
    
      fl::true_type: template <typename T=void> struct enable_if : redirect<T> {};
      fl::false_type: template <typename T=void> struct enable_if {};
      using: template <typename T=void> using enable_if_t = typename enable_if<T>::type;
---
<a name="fl\:\:true_type\\false_type\:\:AndOrXorNot"></a>
###### - And(_b) Or(_b) Xor(_b) Not(_b)

    // Simulate logical operator of bool with the type
    Ex: <tf_type1, tf_type2> tf_type1::And<tf_type2>::Not == !(tf_type1 && tf_type2) 
    Ex: <tf_type1, tf_type2> tf_type1::Or<tf_type2::Not> == tf_type1 || !tf_type2 
    Ex: <tf_type1, tf_type2> tf_type1::Xor<tf_type2> == tf_type1 ^ tf_type2


<a name="#fl\:\:Bool"></a>
### `fl::Bool`
fl\:\:Bool\<bool b\> convert a constexpr bool into fl::true_type of fl::false_type

    template <bool b> struct Bool : redirect_type<std::conditional<b, fl::true_type, fl::false_type>> {};
    using: template <bool b> using Bool_t = typename Bool<b>::type;


<a name="#fl\:\:is"></a>
## namespace fl\:\:is
<a name="#fl\:\:is\:\:none"></a>
### `fl::is::none`
fl\:\:is\:\:none<T> redirect true or false inside of value if the template is of the class fl\:\:none or not

    template<typename T> struct is::none : std::conditional<std::is_same<T, fl::none>::value, fl::true_type, fl::false_type>::type {};
    C++ 17, inline constexpr: template <typename T> inline static constexpr bool none_v = none<T>::value;


<a name="#fl\:\:is\:\:undefined"></a>
### `fl::is::undefined`
fl\:\:is\:\:undefined<T> inherit fl::true_type if T is of a specialization of ref else fl::false_type

    template<typename T> struct is::undefined : std::conditional<std::is_same<T, fl::undefined>::value, fl::true_type, fl::false_type>::type {};
    C++ 17, inline constexpr: template <typename T> inline static constexpr bool is::undefined_v = is::undefined<T>::value;


<a name="#fl\:\:is\:\:specialization"></a>
### `fl::is::specialization`
fl\:\:is\:\:specialization<T, <...> Ref> inherit fl::true_type if T is of a specialization of ref else fl::false_type

    Ex: fl::is::specialization<std::vector<int>, std::vector>::value == true

    C++ 17, inline constexpr: template <typename T, template<typename...> class Ref> inline static constexpr bool specialization_v = specialization<T, Ref>::value;
    

readme.md not finished