# ASP.NET Core, Dapper ORM and MySql
A simple example project using ASP.NET Core, Dapper ORM and MySQL.

I met .Net Core and Dapper ORM through my work buddy [@thiagodds](https://github.com/thiagodds). Here's my thank you for you. :+1:

```
This tutorial is under construction!
```


What you need to do
-------------------

First, install the following applications:
- [.NET Core SDK](https://www.microsoft.com/net/download/core)
- [Visual Studio Code](https://code.visualstudio.com/)
- [C# for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
- [MySQL Community Edition](https://dev.mysql.com/downloads/mysql/)

Considering that you know a little about .Net Core, open the 'Visual Studio Code' and create a new .NET Core web project.

Apply the nuget packages listed below:
- [Dapper](https://www.nuget.org/packages/Dapper)
- [Dapper.Contrib](https://www.nuget.org/packages/Dapper.Contrib/)
- [MySqlConnector](https://www.nuget.org/packages/MySqlConnector/)

Open the file 'appsettings.json' and put yours MySQL's configurations like the example below:

```json
  "ConnectionStrings": {
    "ConnectionString1": "host=localhost;port=3306;user id=USER;password=PASSWORD;database=DATABASENAME;"
  }
```

Create a directory called 'Code' in the root folder of your project and in this folder create a class file called 'ConnectionStringList.cs' with the following content:

```csharp
namespace AspNetCoreDapperMySql.Code
{
    public class ConnectionStringList
    {
        public string ConnectionString1 { get; set; }
    }
}
```

Now, create other directory called 'Models' and three classes files with the following contents:

`Pais.cs`
```csharp
using Dapper.Contrib.Extensions;
using System.Collections.Generic;

namespace AspNetCoreDapperMySql.Models
{
    [Table("GLB_Pais")]
    public class Pais
    {
        [Key]
        public int Id { get; set; }
        public string Nome { get; set; }
        public ICollection<Uf> Ufs { get; set; }
    }
}
```

`Uf.cs`
```csharp
using Dapper.Contrib.Extensions;
using System.Collections.Generic;

namespace AspNetCoreDapperMySql.Models
{
    [Table("GLB_UF")]
    public class Uf
    {
        [Key]
        public int Id { get; set; }
        public int Id_GLB_Pais { get; set; }
        public string Nome { get; set; }
        public string Sigla { get; set; }
        public Pais Pais { get; set; }
        public ICollection<Cidade> Cidades { get; set; }
    }
}
```

`Cidade.cs`
```csharp
using Dapper.Contrib.Extensions;

namespace AspNetCoreDapperMySql.Models
{
    [Table("GLB_Cidade")]
    public class Cidade
    {
        [Key]
        public int Id { get; set; }
        public int Id_GLB_UF { get; set; }
        public string Nome { get; set; }
        public Uf Uf { get; set; }
    }
}
```

And, create another directory called 'Repository' and two classes files with the following contents:

`IRepositoryBase.cs`
```csharp
using AspNetCoreDapperMySql.Models;
using System.Collections.Generic;

namespace AspNetCoreDapperMySql.Repository
{
    internal interface IRepositoryBase
    {
        List<Cidade> SearchCidades(string nome);

        List<Uf> SearchUfs(string nome);

        List<Pais> SearchPaises(string nome);
    }
}
```

`RepositoryBase.cs`
```csharp
using AspNetCoreDapperMySql.Code;
using AspNetCoreDapperMySql.Models;
using Dapper;
using Microsoft.Extensions.Options;
using MySql.Data.MySqlClient;
using System.Collections.Generic;
using System.Data;
using System.Data.SqlClient;
using System.Linq;

namespace AspNetCoreDapperMySql.Repository
{
    public class RepositoryBase : IRepositoryBase
    {
        private readonly IDbConnection _db;

        public RepositoryBase(IOptions<ConnectionStringList> connectionStrings)
        {
            _db = new MySqlConnection(connectionStrings.Value.ConnectionString1);
        }

        public void Dispose()
        {
            _db.Close();
        }

        public List<Cidade> SearchCidades(string nome)
        {
            nome = "Juiz de Fora";

            if (string.IsNullOrEmpty(nome))
                return _db.Query<Cidade>("SELECT * FROM GLB_Cidade ORDER BY Nome ASC LIMIT 10").ToList();

            nome = nome.Trim();

            return _db.Query<Cidade>("SELECT * FROM GLB_Cidade WHERE Nome LIKE @Nome ORDER BY Nome ASC LIMIT 10", new { Nome = string.Format("%{0}%", nome) }).ToList();
        }

        public List<Uf> SearchUfs(string nome)
        {
            if (string.IsNullOrEmpty(nome))
                return _db.Query<Uf>("SELECT * FROM GLB_UF ORDER BY Nome ASC LIMIT 10").ToList();

            nome = nome.Trim();

            return _db.Query<Uf>("SELECT * FROM GLB_UF WHERE Nome LIKE @Nome ORDER BY Nome ASC LIMIT 10", new { Nome = string.Format("%{0}%", nome) }).ToList();
        }

        public List<Pais> SearchPaises(string nome)
        {
            if (string.IsNullOrEmpty(nome))
                return _db.Query<Pais>("SELECT * FROM GLB_Pais ORDER BY Nome ASC LIMIT 10").ToList();

            nome = nome.Trim();

            return _db.Query<Pais>("SELECT * FROM GLB_Pais WHERE Nome LIKE @Nome ORDER BY Nome ASC LIMIT 10", new { Nome = string.Format("%{0}%", nome) }).ToList();
        }
    }
}
```


Nuget packages applied
----------------------

- [Dapper](https://www.nuget.org/packages/Dapper)
- [Dapper.Contrib](https://www.nuget.org/packages/Dapper.Contrib/)
- [MySqlConnector](https://www.nuget.org/packages/MySqlConnector/)


License
-------

This example application is [MIT Licensed](https://github.com/anzolin/AspNetCoreDapperMySql/blob/master/LICENSE).


About the author
----------------

Hello everyone, my name is Diego Anzolin Ferreira. I'm a .NET developer from Brazil. I hope you will enjoy this simple example application as much as I enjoy developing it. If you have any problems, you can post a [GitHub issue](https://github.com/anzolin/AspNetCoreDapperMySql/issues). You can reach me out at diego@anzolin.com.br.
