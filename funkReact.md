Create a simple Angular or React component according to the following criteria:

1. It should contain one input, one button, and an area to display a value
2. When the button is clicked, it should generate a random number. If that number is even, it should display the number in the display area. If it is odd, it should not display the number.
3. When the button is clicked, if the input has a value of "Rally" or "rally" (or any combination of capitalization), it should change the background color of the display area to Rally orange (#F26C32) and the text to white.
4. The component itself should have one piece of data passed in - an integer between 0 and 100 corresponding to the opacity of the display area. If the value is undefined, it should default to 100% but still apply that styling.
   React Boilerplate:
   class Rally extends Component {
   render() {
   return (
   // content
   );
   }
   export default Rally;
