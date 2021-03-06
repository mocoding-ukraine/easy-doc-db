# Easy-Doc-Db

![Build Status](https://mocoding.visualstudio.com/_apis/public/build/definitions/da7703d4-fb22-4933-b869-83f4264b7b84/29/badge)
[![NuGet version](https://badge.fury.io/nu/Mocoding.EasyDocDb.svg)](https://www.nuget.org/packages/Mocoding.EasyDocDb)

easy-doc-db - embedded nosql document storage database for .NET. It is best suited for small or medium-sized projects with limited or predictably small amount of data. All data are kept in memory while changes are persisted to specified storage as json, xml or yaml formatted documents.

Key benefits:
 - Read optimized
 - No data or schema migrations
 - Extendable
 - Thread-safe

Demo:

```cs
IRepository repository = new Repository(new JsonSerializer(),new EmbeddedStorage()); 
var users = await repository.InitCollection<User>("../data/users");

// read all
var allDocs = users.Documents.Select(u => u.Data);

//...
// create new
var newUser = users.New();
newUser.Data.FirstName = "Name1";
newUser.Data.LastName = "LastName1";
await newUser.Save();

//...
//delete
await newUser.Delete();
```

"../data/users" – path for storing a collection of documents. 
Each document will be saved as a plain JSON file. XML and YAML are also supported.

Please see more demo in the examples folder.

### Supported platforms

We support next platforms:

- .NET Standard 1.0 for Core Library and JSON/XML/YAML Serialializers
- .NET Standard 1.3 for FileSystem Repsitory

Supporting .NET Standard 1.0 is essential since it is supported by widest range of target platforms including Xamarin, .NET Core and native .NET Framework.

## Usage Details

IRepository supports working with a single document or collection of documents.

```cs
Task<IDocument<T>> Init<T>(string conn) where T : class, new();
```
 - will load or create a new document at the location specified by the input parameter.

```cs
Task<IDocumentCollection<T>> InitCollection<T>(string conn) where T : class, new();
```
- will create a new folder at the location specified by the input parameter.
All files will go there using the following format: {guid}.{format}.

IDocumentCollection provides access to all documents and allows creating a new one.

```cs
ImmutableArray<IDocument<T>> Documents { get; }
IDocument<T> New(T initialData = null);
```

IDocument allows saving, deleting and updating the document in a thread safe manner.
  
 ```cs
T Data { get; }
Task SyncUpdate(Action<T> data);
Task SyncUpdate(T data);
Task Save();
Task Delete();
```
NOTE: Data property is not thread safe.

## Extend
 
### Serializers

There are currently 3 serializers availible: JSON, YAML and XML. They are availible via their nugets:
- Mocoding.EasyDocDb.Json
- Mocoding.EasyDocDb.Yaml
- Mocoding.EasyDocDb.Xml

For a custom serializer you need to implement IDocumentSerializer interface and pass it to Repository constructor.


```cs
public interface IDocumentSerializer
{
    string Type { get; }
    string Serialize<T>(T data) where T : class, new();
    T Deserialize<T>(string content) where T : class, new();
}
```

### Storage

Mocoding.EasyDocDb.FileSystem provides default implementation that stores documents to one of your local folders.
The default behavior could be overridden by a custom implementation of IDocumentStorage interface.
Below, we will show ysou methods for reading, writing, deleting and other operations with document.

```cs
public interface IDocumentStorage
{
   Task<string> Read(string @ref);
   Task Write(string @ref, string document);
   Task Delete(string @ref);
   Task<string[]> Enumerate(string collectionRef);
   string NewRef(string collectionRef, string name);
}
```

## Contributions

All source code is located in ```src``` folder. Tests are in ```test``` folder. You will need Visual Studio 2015 Update 3.
You may use examples to play with and/or improve existing code base.

## Contact Us

Our website: [http://mocoding.com](http://mocoding.com)

Email: [social@mocoding.com](mailto:social@mocoding.com)