
## A toast notifier for Visual RPG Windows applications

The proverbial message box is usually the go-to for showing users a message in an ASNA Visual RPG (AVR) Windows application. The message box is flawed for several reasons: 

* It's modal--users can't do anything else until the message box's `OK` button is clicked.
* It's dumb. It always displays in center of the Windows desktop.This means that often your users' attention is on your app and the message you want them to see is out somewhere in the desktop's south 40. This not only inhibits user comprehension, but is also annoying because users have to find it to click it to continue their work.
* You have minimal control over its cosmetics
* It looks stupid and lazy. 

Figure 1 below shows a message box way out in the weeds (this image is the upper left-hand corner of the desktop). This message box is pretty obvious because of the empty desktop--but when that desktop is busy, the message box can be hard for a user to find.

![downloads/images/stupid-msgbox.png](https://asna.com/downloads/images/stupid-msgbox.png)

<small>Figure 1. A dumber-than-a-sack-of-hammers traditional Windows message box</small>

There must be a better way. Many years ago, a user interface element called a "Toast" (because it pops up) became very popular. These asynchronous message boxes show the user a message and then usually, after a specified number of seconds, disappear on their own.

Don't confuse a toast notification with a modal dialog. They serve different purposes. Toasts are for brief user notification; modal dialogs accept user input or choices (ie OK or Cancel).

An example toast notification is shown below in Figure 2.

![downloads/images/growler-popup.png](https://asna.com/downloads/images/growler-popup.png)


<small>Figure 2. An example toast notification</small>


### A programming cheat

.NET Framework doesn't intrinsically provide a toast notification control. This article provides an effective hack to give your ASNA Visual RPG (AVR) Windows programs asynchronous toast messages. The "asynchronous" part of this toast implementation is a bit of a cheat. The toast container is just a Windows form with its properties tweaked. Using a form for this "control" gets us asynchronous behavior for free by way of the form's `Show` method. The form's `FormBorderStyle` is set to 'None' to mask the fact that toast is really just a form. 

Using a form for the toast presents one minor problem. If the user gives the parent form focus while a toast is displayed, the toast disappears behind that parent form. To resolve this, the toast form's `TopMost` property is set to `*True.` Given that toasts are displayed for only a few seconds, this shouldn't cause any issues.

This article's accompanying example AVR project provides a way to test the toast notifier, show below in Figure 3. 

![downloads/images/popup-message.gif](https://asna.com/downloads/images/popup-message.gif)

<small>Figure 3. A form is provided to test the toast notifier. </small>

The toast notifier is self-contained in the accompanying example's Toast form. It surfaces several properties you set before showing the form. The code below in Figure 4 displays the toast shown in the upper left-hand corner of the parent form in Figure 2. To use the toast notifier in your projects, just add the Toast form to your project and use the code below.

```
DclFld g Type(Toast) New(*This) 

t.HeaderMessage = 'Success'
t.Message = 'Your file was saved.'
t.MessageStatus = g.MessageStatus.SUCCESS
t.Position = t.Position.UPPER_LEFT
t.Show()
```

<small>Figure 4. Figure example code to show a toast</small>

When you instance the `Toast` form, note that you must pass this `*This` to the constructor (which passes a reference to the parent form to the toast form). This provides access to the parent form's properties to the toast notifier. Having those parent properties easily available is what enables the toast to position itself relative to its parent form. 

### Toast properties

|Property | Type | Value |Default|
|:---------|:-------|:---|:---|
| Header Message   | *String                 | A string to display toast header text  | No default           |
| Message          | *String                 | The toast text                         | No default           |
| HideHeader       | *Boolean                | Set to true to hide the toast header   | *False               |
| ToastStatus      | Enumeration             | Success, Warning, Failure              | Status.SUCCESS       |
| BorderColor      | System.Drawing.Color    | Toast border color                     | Color.LightSteelBlue |
| ToastPosition    | Enumeration             | See below                              | Position.LOWER_LEFT  |
| SecondsDisplayed | *Integer4               | Seconds the toast is displayed         | 2.5 seconds          |

The ToastStatus and ToastPositions are enumeration values. The ToastStatus values are:

* ToastStatus.SUCCESS -- always displayed with a green header background (default)
* ToastStatus.WARNING -- always displayed with a yellow header background
* ToastStatus.FAILURE -- always displayed with a red header background 

The ToastPosition values are:
* ToastPosition.UPPER_LEFT
* ToastPosition.UPPER_RIGHT
* ToastPosition.LOWER_LEFT (default)
* ToastPosition.LOWER_RIGHT

### A quick aside

Programmers are notorious for solving the wrong problem. One of my favorite examples of this is something that Nathan Myhrvold did. Nathan was Microsoft's Chief Technology Office for several years. He left Microsoft to pursue his passion for cooking (check out his [$500 set of cookbooks!](https://www.amazon.com/Modernist-Cuisine-Science-Stainless-Slipcase/dp/1734386142/ref=sr_1_1?crid=1BOC9DXW29YFN&keywords=modernist+cuisine&qid=1679078125&sprefix=modernist+cuisine%2Caps%2C107&sr=8-1)). In 2017 Nathan got [some attention](https://www.cnet.com/culture/internet/nathan-myhrvold-microsoft-everything-bagels-modernist-bread/) when he figured out how to make an [everything bagel](https://en.wikipedia.org/wiki/Everything_bagel) with everything on both halves of the bagel. 

I think Nathan solved the wrong problem. He toiled over many recipes to get the "everything" mixture to stick to both bagel halves. That problem can be easily resolved by mixing yourself up some everthing seasoning with cream cheese just before you eat the bagel (timing is important, mixing "everything" with the cream cheese to far in advance lessens everything's delicious crunch). This gets you everything on both bagels, and if you happen to be doing this with a conventional everything bagel, you get double everything on half! What could be better?

The same the mistake was made with this toast notifier. I wrote it one night while my wife was watching Game of Thrones (and I pretended to be watching it with her--I hated that show!). It was one of those projects that just sort of started itself and without a lot of planning, boom, it worked for the challenge I had. 

Alas, in retrospect I realized that I solved the wrong problem with this toast project. What I should have written was a general purpose form-fader. That would have provided a more flexible solution-- one that would work with both modal dialogs, asynchronous toast-type notifiers, and even regular forms. 

I'm going to leaving resolving that broader challenge to you. In the meantime, make yourself some toast with this little project. It will improve the user experience of your Visual RPG Windows applications.

