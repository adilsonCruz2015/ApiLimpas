# Api Limpa

Recursos necesários para ter uma Api limpa


```sh
Microsoft.AspNet.WebApi.Owin
```

```sh
Microsoft.Owin.Host.SystemWeb
```


# Pacote para dizer quem vai acessar sua api

```sh
Microsoft.Owin.Cors
```

# Compactar o retorno de cada requisição realizada para api


```sh
Microsoft.AspNet.WebApi.Extensions.Compression.Server
```


# Com os pacotes acima instalados.

Vamos criar uma classe que sera o responsável por levantar nossa Api.
Essa classe será a: 

```sh
Startup
```

```sh

public void Configuration(IAppBuilder app)
{
	HttpConfiguration config = new HttpConfiguration();

	WebApiConfig.Register(config);
	JsonConfig.Register(config);

	app.UseCors(Microsoft.Owin.Cors.CorsOptions.AllowAll);
	app.UseWebApi(config);
}

```


Configurar as rotas

Eu criei uma classe static para armazenar as configurações da rota

```sh
WebApiConfig
```

```sh

public static class WebApiConfig
{
	public static void Register(HttpConfiguration config)
	{
		config.MapHttpAttributeRoutes();

		config.Routes.MapHttpRoute(
			name: "DefaultApi",
			routeTemplate: "api/{controller}/{id}",
			defaults: new { id = RouteParameter.Optional}
		);

		// Permite exibir as mensagens de erro internas do servidor
		config.IncludeErrorDetailPolicy = IncludeErrorDetailPolicy.Always;
	}
}
		
```

Configurar o retorno. No caso escolhi Json

Eu criei uma classe static para armazenar as configurações do Json.

```sh
JsonConfig
```

```sh

public static class JsonConfig
{
	public static void Register(HttpConfiguration config)
	{
		config.Formatters.Remove(config.Formatters.XmlFormatter);

		//compacta retorno de cada requisição realizada para api.
		config.MessageHandlers.Insert(0, new ServerCompressionHandler(new GZipCompressor(), new DeflateCompressor()));

		var json = config.Formatters.JsonFormatter;

		json.SupportedMediaTypes.Clear();
		json.SupportedMediaTypes.Add(new MediaTypeHeaderValue("text/json"));

		json.SerializerSettings.PreserveReferencesHandling = PreserveReferencesHandling.None;
		json.SerializerSettings.ReferenceLoopHandling = ReferenceLoopHandling.Ignore;
		json.SerializerSettings.Culture = System.Globalization.CultureInfo.CurrentCulture;


		json.SerializerSettings.ContractResolver = new CamelCasePropertyNamesContractResolver();
	}
}

```

# Com as configurações acima estamos quase prontos para rodar uma api. Falta criar a controller.


```sh
CargoController
```

Aqui abaixo um exemplo muito básico de criar uma ApiController

```sh

[RoutePrefix("Cargo")]
public class CargoController : ApiController
{
	[HttpPost]
	public async Task<HttpResponseMessage> Post([FromBody] string parametros)
	{
		if(!object.Equals(parametros, null))
		{
			return  Request.CreateResponse(HttpStatusCode.OK, parametros);
		}
		else
		{
			return Request.CreateResponse(HttpStatusCode.BadRequest, "Erro");
		}
	}
}

```

Com as informações acima, ja podemos rodar uma api com informações basico.


# Para parte de sugurança e geração de Token instalar 

```sh
Microsoft.Owin.Security.OAuth
```


