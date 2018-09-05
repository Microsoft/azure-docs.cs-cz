## <a name="append-other-apis"></a>Připojení dalších rozhraní API

Rozhraní API se může skládat z rozhraní API vystavovaných různými službami, včetně specifikace OpenAPI, rozhraní API SOAP, funkce API Apps služby Azure App Service, aplikace Azure Function App, Azure Logic Apps a Azure Service Fabric.

![Import rozhraní API](./media/api-management-append-apis/import.png)

Pokud chcete k existujícímu rozhraní API připojit ještě jiné rozhraní API, proveďte následující kroky. Při importu dalšího rozhraní API se k aktuálnímu rozhraní API připojí příslušné operace.

1. Na webu Azure Portal přejděte ke své instanci služby Azure API Management.
2. V nabídce na levé straně vyberte **Rozhraní API**.
3. Klikněte na **…** vedle rozhraní API, ke kterému chcete připojit jiné rozhraní API.
4. V rozevírací nabídce vyberte **Importovat**.
5. Vyberte službu, ze které se má importovat rozhraní API.