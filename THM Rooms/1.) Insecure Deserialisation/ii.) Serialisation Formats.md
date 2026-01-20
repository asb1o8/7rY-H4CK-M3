## Serialisation Formats

- The underlying principle in all programming languages remains the same. Whether Java, Python, .NET, or PHP, each
  language implements serialisation to accommodate specific features or security measures inherent to its environment.



  ### 🚩 PHP Serialisation:- 
- In PHP, serialisation is accomplished using the serialize() function. This function converts a PHP object or
  array into a byte stream representing the object's data and structure. The resulting byte stream can include
  various data types, such as strings, arrays, and objects, making it unique.
- To illustrate this, let's consider a notes application where users can save and retrieve their notes. We'll
  create a PHP class called Notes to represent each note and handle serialisation and deserialisation.
 Example: 
           
      class Notes {
      public $Notescontent;

                  public function __construct($content) {
                  $this->Notescontent = $content;
                  }
               }

        
In our Notes application, when a user saves a note, we serialise the Notes class object using PHP's serialize() 
function. This converts the object into a string representation that can be stored in a file or database. Let's 
take a look at the following code snippet that serialises the Notes class object:

    $note = new Notes("Welcome to THM");
    $serialized_note = serialize($note);

        

