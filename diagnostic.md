# Rails API Relationships Diagnostic

Place your responses inside the fenced code-blocks where indivated by comments.

1.  Describe a reason why a join tables may be valuable.

  ```md
  Join tables are valuable in part because it allows a little more flexibility/provides more clarity about what you're doing. Using a join table is preferable to having a has-and-belongs-to-many relationship because that type of relationship is limited to exactly the two types of data in that relationship... a join table is less expensive, and it makes it easier to build on relationships if you decide to add more data.
  ```

1.  Provide a database table structure and explain the Entity Relationship that
  describes a many-to-many relationship for `Profiles`, `Movies` and `Favorites`
  (Think of Netflix). A `Profile` has a `given_name`, `surname` and `email` and a
  `Movies` have `title`, `release_date`, and `length` and `Favorites` would be the
  join table with references to `Movies` and `Profiles`.

  ```md

    A `Profile` has many `Favorites` and has many `Movies` through `Favorites`...
    A `Movie` also has many `Favorites` and has many `Profiles` through `Favorites`...
    `Favorites` belongs to many `Movies` and `Profiles`.
  ```

1.  For the above example, what needs to be added to the Model files?

  ```rb
  class Profile < ActiveRecord::Base
    has_many :movies, through: :favorites
    has_many :favorites

    validates :given_name, presence: true
    validates :surname, presence: true
    validates :email, presence: true
  end
  ```

  ```rb
  class Movie < ActiveRecord::Base
    has_many :profiles, through: :favorites
    has_many :favorites

    validates :title, presence: true
    validates :release_date, presence: true
    validates :length, presence: true
  end
  ```

  ```rb
  class Favorite < ActiveRecord::Base
    belongs_to :profile
    belongs_to :movie
    validates :profile, presence: true
    validates :movie, presence: true
  end
  ```

1.  What is the purpose of a serializer? What would our `Profile` serializer look
like to show all movies favorited by a profile on
`http://localhost:3000/profiles/1`

  ```md
  Serializers filter and reshape data for what the server can send out or receive.
  ```

  ```rb
  class ProfileSerializer < ActiveModel::Serializer
    attributes :id, :given_name, :surname, :email, :movies

    def movies
      object.movies.pluck(id)
    end
  end
  ```

1.  What would the command be to _scaffold_ out a **join table** for Favorites from
the above `Movies` and `Profiles`.

  ```sh
    bin/rails generate scaffold favorite profile:references movie:references
  ```

1.  What is `Dependent: Destroy` and where/why would we use it?

  ```md
    I think it's when we delete a row on the join table? So in this case we'd use it if a user decided she didn't like a movie anymore and wanted to remove it from her Favorites.
  ```

1.  Think of **ANY** example where you would have a one-to-many relationship as well
as a many-to-many relationship in an application. You only need to list the
description about the resources and how they relate to one another.

  ```md
  (I guess if we pretend collaborative works aren't considered books) One author can have many books, but books tend to only belong to one author (one-to-many)... But in a library we also have borrowers, and one borrower can borrow many books (through a loan), and any given book can have multiple borrowers (through loans), so that would be many-to-many.
  ```
