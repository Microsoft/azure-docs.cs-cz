---
title: Implementace zotavení po havárii pomocí zálohování a obnovení ve správě rozhraní API
titleSuffix: Azure API Management
description: Zjistěte, jak pomocí zálohování a obnovení provádět zotavení po havárii ve správě rozhraní Azure API.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/03/2020
ms.author: apimpm
ms.openlocfilehash: e74d7dcf8764d167e0080c9d7cca5573bd69ef1d
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260985"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Implementace zotavení po havárii pomocí zálohování a obnovení služby ve službě Azure API Management

Publikováním a správou rozhraní API prostřednictvím azure api managementu využíváte možnosti odolnosti proti chybám a infrastruktury, které byste jinak navrhovali, implementovali a spravovali ručně. Platforma Azure zmírňuje velký zlomek potenciálních selhání za zlomek nákladů.

Chcete-li se zotavit z problémů s dostupností, které ovlivňují oblast, která hostuje službu api management, buďte kdykoli připraveni obnovit službu v jiné oblasti. V závislosti na účelu doby obnovení můžete chtít zachovat pohotovostní službu v jedné nebo více oblastech. Můžete se také pokusit zachovat jejich konfiguraci a obsah v synchronizaci s aktivní službou podle cíle bodu obnovení. Funkce zálohování a obnovení služby poskytuje nezbytné stavební kameny pro implementaci strategie zotavení po havárii.

Operace zálohování a obnovení lze také použít pro replikaci konfigurace služby API Management mezi provozními prostředími, například pro vývoj a přípravu. Dejte si pozor na to, aby se zkopírovala také data za běhu, jako jsou uživatelé a předplatná, což nemusí být vždy žádoucí.

Tato příručka ukazuje, jak automatizovat operace zálohování a obnovení a jak zajistit úspěšné ověřování požadavků na zálohování a obnovení pomocí Správce prostředků Azure.

> [!IMPORTANT]
> Operace obnovení nezmění vlastní konfiguraci názvu hostitele cílové služby. Doporučujeme použít stejný vlastní název hostitele a certifikát TLS pro aktivní i pohotovostní služby, aby po dokončení operace obnovení mohl být provoz přesměrován na pohotovostní instanci jednoduchou změnou CNAME DNS.
>
> Operace zálohování nezachycuje předem agregovaná data protokolu použitá v sestavách zobrazených v okně Analytics na webu Azure Portal.

> [!WARNING]
> Platnost každé zálohy vyprší po 30 dnech. Pokud se pokusíte obnovit zálohu po uplynutí 30denní doby vypršení platnosti, obnovení se nezdaří se zprávou. `Cannot restore: backup expired`

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="authenticating-azure-resource-manager-requests"></a>Ověřování požadavků Azure Resource Manageru

> [!IMPORTANT]
> Rozhraní REST API pro zálohování a obnovení používá Azure Resource Manager a má jiný mechanismus ověřování než rozhraní API REST pro správu entit správy rozhraní API. Kroky v této části popisují, jak ověřit požadavky Azure Resource Manager. Další informace naleznete [v tématu Ověřování požadavků Správce prostředků Azure](/rest/api/index).

Všechny úkoly, které provedete u prostředků používajících Správce prostředků Azure, musí být ověřeny pomocí služby Azure Active Directory pomocí následujících kroků:

-   Přidejte aplikaci do klienta Služby Azure Active Directory.
-   Nastavte oprávnění pro aplikaci, kterou jste přidali.
-   Získejte token pro ověřování požadavků na Azure Resource Manager.

### <a name="create-an-azure-active-directory-application"></a>Vytvoření aplikace Azure Active Directory

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Pomocí předplatného, které obsahuje instanci **služby** Api Management, přejděte na kartu Registrace aplikací ve **službě Azure Active Directory** (Azure Active Directory > registrace správou a aplikací).

    > [!NOTE]
    > Pokud výchozí adresář Služby Azure Active Directory není viditelný pro váš účet, obraťte se na správce předplatného Azure a udělte požadované oprávnění vašemu účtu.

3. Klikněte na **Registrace nové aplikace**.

    Vpravo se zobrazí okno **Vytvořit.** To je místo, kde zadáte aplikace AAD relevantní informace.

4. Zadejte název aplikace.
5. Pro typ aplikace vyberte **nativní**.
6. Zadejte zástupnou adresu `http://resources` URL, například pro **identifikátor URI přesměrování**, protože se jedná o povinné pole, ale hodnota se později nepoužije. Kliknutím na zaškrtávací políčko aplikaci uložíte.
7. Klikněte na **Vytvořit**.

### <a name="add-an-application"></a>Přidání aplikace

1. Po vytvoření aplikace klepněte na tlačítko **Oprávnění rozhraní API**.
2. Klepněte na tlačítko **+ Přidat oprávnění**.
4. Stiskněte **klávesu Select Microsoft API**.
5. Zvolte **Azure Service Management**.
6. Stiskněte **klávesu Select**.

    ![Přidání oprávnění](./media/api-management-howto-disaster-recovery-backup-restore/add-app.png)

7. Vedle nově přidané aplikace klikněte na **Delegovaná oprávnění** a zaškrtněte políčko **Access Azure Service Management (preview).**
8. Stiskněte **klávesu Select**.
9. Klepněte na **tlačítko Udělit oprávnění**.

### <a name="configuring-your-app"></a>Konfigurace aplikace

Před voláním rozhraní API, které generují zálohu a obnovují ji, je třeba získat token. Následující příklad používá balíček [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet k načtení tokenu.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace GetTokenResourceManagerRequests
{
    class Program
    {
        static void Main(string[] args)
        {
            var authenticationContext = new AuthenticationContext("https://login.microsoftonline.com/{tenant id}");
            var result = authenticationContext.AcquireTokenAsync("https://management.azure.com/", "{application id}", new Uri("{redirect uri}"), new PlatformParameters(PromptBehavior.Auto)).Result;

            if (result == null) {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }

            Console.WriteLine(result.AccessToken);

            Console.ReadLine();
        }
    }
}
```

`{tenant id}`Vyměňte `{application id}`, `{redirect uri}` a podle následujících pokynů:

1. Nahraďte `{tenant id}` ID klienta vytvořené aplikace Azure Active Directory. K ID se dostanete kliknutím na**koncové body** **registrace** -> aplikací .

    ![Koncové body][api-management-endpoint]

2. Nahraďte `{application id}` se hodnotou, kterou získáte, přejděte na stránku **Nastavení.**
3. Nahraďte hodnotu z karty **Přesměrování identifikátorů URI** aplikace Azure Active Directory. `{redirect uri}`

    Jakmile jsou zadány hodnoty, příklad kódu by měl vrátit token podobný následujícímu příkladu:

    ![Podpisový][api-management-arm-token]

    > [!NOTE]
    > Platnost tokenu může vypršet po určité době. Spusťte ukázku kódu znovu generovat nový token.

## <a name="calling-the-backup-and-restore-operations"></a>Volání operací zálohování a obnovení

Rozhraní REST API jsou [služba api management service – služba zálohování](/rest/api/apimanagement/2019-12-01/apimanagementservice/backup) a správy rozhraní API – [obnovení](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore).

Před voláním operace zálohování a obnovení popsané v následujících částech nastavte hlavičku žádosti o autorizaci pro volání REST.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="back-up-an-api-management-service"></a><a name="step1"> </a>Zálohování služby api management

Chcete-li zálohovat problém služby správy rozhraní API, následující požadavek HTTP:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

kde:

-   `subscriptionId`- ID předplatného, které obsahuje službu API Management, kterou se pokoušíte zálohovat
-   `resourceGroupName`- název skupiny prostředků služby Azure API Management
-   `serviceName`- název služby API Management, kterou vytváříte v době jejího vzniku, je určen
-   `api-version`- nahradit`2018-06-01-preview`

V textu požadavku zadejte název cílového účtu úložiště Azure, přístupový klíč, název kontejneru objektů blob a název zálohy:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Nastavte hodnotu `Content-Type` hlavičky `application/json`požadavku na .

Zálohování je dlouho běžící operace, která může trvat déle než minutu. Pokud byl požadavek úspěšný a proces zálohování `202 Accepted` byl zahájen, `Location` obdržíte kód stavu odpovědi s hlavičkou. Proveďte požadavky 'GET' na `Location` adresu URL v záhlaví zjistit stav operace. Během zálohování se nadále zobrazuje stavový kód 202 Přijato. Kód odpovědi `200 OK` označuje úspěšné dokončení operace zálohování.

Při vytváření žádosti o zálohování nebo obnovení si všimněte následujících omezení:

-   **Kontejner** zadaný v těle požadavku **musí existovat**.
-   Během zálohování se **vyhněte změnám správy ve službě,** jako je například upgrade nebo downgrade skladové položky, změna názvu domény a další.
-   Obnovení **zálohy je zaručeno pouze po dobu 30 dnů** od okamžiku jejího vytvoření.
-   **Data o využití** použitá k vytváření analytických sestav nejsou v záloze **zahrnuta.** Pomocí [rozhraní REST API azure api][azure api management rest api] pro pravidelné načítání analytických sestav pro bezpečné vedení.
-   Kromě toho následující položky nejsou součástí záložních dat: vlastní certifikáty TLS/SSL domény a všechny zprostředkující nebo kořenové certifikáty nahrané zákazníkem, obsah portálu pro vývojáře a nastavení integrace virtuální sítě.
-   Frekvence, s jakou provádíte zálohování služeb, ovlivňuje cíl bodu obnovení. Chcete-li ji minimalizovat, doporučujeme implementovat pravidelné zálohování a provádět zálohování na vyžádání po provedení změn ve službě api management.
-   **Změny** provedené v konfiguraci služby (například rozhraní API, zásady a vzhled portálu pro vývojáře) během operace zálohování **mohou být vyloučeny ze zálohy a budou ztraceny**.
-   **Povolit** přístup z roviny řízení k účtu úložiště Azure, pokud má [povolenou bránu firewall.][azure-storage-ip-firewall] Zákazník by měl otevřít sadu [IP adres roviny řízení řízení rozhraní Api Azure][control-plane-ip-address] ve svém účtu úložiště pro zálohování nebo obnovení z. 

> [!NOTE]
> Pokud se pokusíte provést zálohování nebo obnovení ze služby správy rozhraní API nebo do ní pomocí účtu úložiště, který má povolenou [bránu firewall,][azure-storage-ip-firewall] ve stejné oblasti Azure, pak to nebude fungovat. Důvodem je, že požadavky na Azure Storage nejsou nanated na veřejnou IP z výpočetní > (Azure Api Management řídicí rovina). Požadavek na úložiště mezi oblastmi bude snated.

### <a name="restore-an-api-management-service"></a><a name="step2"> </a>Obnovení služby správy rozhraní API

Chcete-li obnovit službu správy rozhraní API z dříve vytvořené zálohy, proveďte následující požadavek HTTP:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

kde:

-   `subscriptionId`- ID předplatného, které obsahuje službu API Management, do které obnovujete zálohu
-   `resourceGroupName`- název skupiny prostředků, která obsahuje službu Azure API Management, do které obnovujete zálohu
-   `serviceName`- název služby API Management, která je obnovena, je uveden v době jejího vytvoření
-   `api-version`- nahradit`2018-06-01-preview`

V textu požadavku zadejte umístění záložního souboru. To znamená, že přidejte název účtu úložiště Azure, přístupový klíč, název kontejneru objektů blob a název zálohy:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Nastavte hodnotu `Content-Type` hlavičky `application/json`požadavku na .

Obnovení je dlouho běžící operace, která může trvat až 30 nebo více minut. Pokud byl požadavek úspěšný a proces obnovení `202 Accepted` byl zahájen, `Location` obdržíte kód stavu odpovědi s hlavičkou. Proveďte požadavky 'GET' na `Location` adresu URL v záhlaví zjistit stav operace. Během obnovení se nadále zobrazuje stavový kód 202 Přijato. Kód odpovědi `200 OK` označuje úspěšné dokončení operace obnovení.

> [!IMPORTANT]
> **Skladová položka** obnovené služby do **musí odpovídat** skladové jednotce obnovené zálohované služby.
>
> **Změny** provedené v konfiguraci služby (například API, zásady, vzhled portálu pro vývojáře) během probíhající operace obnovení **mohou být přepsány**.

<!-- Dummy comment added to suppress markdown lint warning -->

> [!NOTE]
> Operace zálohování a obnovení lze provádět také pomocí příkazů PowerShell [_Backup-AzApiManagement_](/powershell/module/az.apimanagement/backup-azapimanagement) a [_Restore-AzApiManagement._](/powershell/module/az.apimanagement/restore-azapimanagement)

## <a name="next-steps"></a>Další kroky

Podívejte se na následující prostředky pro různé návody procesu zálohování a obnovení.

-   [Replikace účtů správy rozhraní API Azure](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
-   [Automatizace zálohování a obnovení služby API Management pomocí Logic Apps](https://github.com/Azure/api-management-samples/tree/master/tutorials/automating-apim-backup-restore-with-logic-apps)
-   [Azure API Management: Zálohování a obnovení konfigurace](https://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
    _Přístup podrobně Stuart neodpovídá oficiální pokyny, ale je to zajímavé._

[backup an api management service]: #step1
[restore an api management service]: #step2
[azure api management rest api]: https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest
[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png
[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
[control-plane-ip-address]: api-management-using-with-vnet.md#control-plane-ips
[azure-storage-ip-firewall]: ../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range
