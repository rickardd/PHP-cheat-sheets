## PHP Dependency Injection

The benefits of using dependency injection

- Keeps the classes Author and Question seperated. 
- The classes are not tight coupled
- Easier to maintain since a change in Author does not have to be updated in Questions
- Easier to unit test. 

class Author {
    private $firstName;
    private $lastName;
     
    public function __construct( String $firstName, String $lastName) {
        $this->firstName = $firstName;
        $this->lastName = $lastName;
    }
 
    public function getFirstName() {
        return $this->firstName;
    }
 
    public function getLastName() {
        return $this->lastName;
    }
}
 
class Question {
    private $author;
    private $question;
 
    public function __construct( String $question, Author $author) {
        $this->author = $author;
        $this->question = $question;
    }
 
    public function getAuthor() {
        return $this->author;
    }
 
    public function getQuestion() {
        return $this->question;
    }
}

$user = new Author('rick', 'dahl');
$q = new Question( 'my question', $user );
echo $user->getFirstName();
echo '<br>';
echo $q->getAuthor()->getFirstName();

