There is a phase in making design decisions when alternatives are placed side-by-side for comparison. [fourup.com](http://fourup.com) is a tool for placing four Web resources into a single browser window.

>This kind of printing, where two pages are printed side-by-side on a piece of paper is called “two-up,” for the two pages facing up. This idea generalizes readily to any number of pages (though, of course, legibility goes down quickly as the number of pages goes up). In its general form, it is called “n-up.”
 >[link](http://www.tailrecursive.org/postscript/nup.html)

The first iteration worked by encoding the resources to compare into the url:

  http://fourup.com/
    ?first=http://arstechnica.com
    &second=http://theverge.com
    &third=http://gizmodo.com
    &fourth=http://engadget.com

  
A second iteration of this experiment is underway, and I hope to describe it in a future post. For now, we'll go into how fourup.com works.

# Parsing the URL String using JavaScript

One of the features for this tool was to allow users to share views easily. By encoding the resulting view into the URL string a user can simply copy and paste the link. Although its not terribly pretty and may present some security concerns, this approach allowed the tool to be prototyped quickly.


  //http://stackoverflow.com/questions/901115/how-can-i-get-query-string-values
  function getParameterByName(name)
  {
    name = name.replace(/[\[]/, "\\\[").replace(/[\]]/, "\\\]");
    var regexS = "[\\?&]" + name + "=([^&#]*)";
    var regex = new RegExp(regexS);
    var results = regex.exec(window.location.search);
    if(results == null)
      return "";
    else
      return decodeURIComponent(results[1].replace(/\+/g, " "));
  }


For example, with `http://example.com/?shoe=brown` in the address bar `getParameterByName(shoe)` would return `brown`. An empty string would be returned otherwise.

# Dynamic site on the cheap

The rest of [fourup.com](http://fourup.com)'s logic is only a few dozen lines of code. We use jQuery selectors to fill the four IFrames on the HTML page based on the values in the query string. Some default values are provided and give fourup.com the effect of having a "home page". However, the `index.html` does little more than parse the query string and load the results.

We first get an array of the query strings we are interested in.

  $(document).ready(function() {
  var fillFrames = function(){
  	urls = [getParameterByName("first"), getParameterByName("second"), getParameterByName("third"), getParameterByName("fourth")];

We then provide some default values if all of the parameters are empty.

  if (urls[0] == "" && urls[1] == "" && urls[2] == "" && urls[3] == "") {
  	$("#first").attr('src',"http://fourup.com/info.html");
  	$("#second").attr('src',"http://fourup.com/gen.html");
  	$("#third").attr('src',"http://fourup.com/4up.jpg");
  	$("#fourth").attr('src',"http://fourup.com/about.txt");
  } else {


If it appears there is a URI in the parameter we can then set the `src` of the IFrame.

  $("#first").attr('src',urls[0]);

Check out [fourup on github](http://github.com/fourup.com) and feel free to fork!

## References

[Two Up](http://www.tailrecursive.org/postscript/nup.html)

[Trying to Validate URL Using JavaScript](http://stackoverflow.com/questions/1303872/trying-to-validate-url-using-javascript)

[How can I get query string values?](http://stackoverflow.com/questions/901115/how-can-i-get-query-string-values)
