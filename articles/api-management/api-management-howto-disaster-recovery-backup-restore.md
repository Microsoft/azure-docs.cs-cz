---
title: Implementace zotavení po havárii pomocí zálohování a obnovení v Azure API Management | Microsoft Docs
description: Přečtěte si, jak pomocí zálohování a obnovení provádět zotavení po havárii v Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/26/2019
ms.author: apimpm
ms.openlocfilehash: 9c97723687484e8af82d63b6fb4999401a69fb2c
ms.sourcegitcommit: 7868d1c40f6feb1abcafbffcddca952438a3472d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71958534"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Implementace zotavení po havárii pomocí zálohování a obnovení služby v Azure API Management

Publikováním a správou vašich rozhraní API prostřednictvím služby Azure API Management využíváte výhody odolnosti proti chybám a možnosti infrastruktury, které byste jinak navrhli, implementovali a spravovali ručně. Platforma Azure snižuje velký podíl potenciálních selhání za zlomek nákladů.

Aby bylo možné obnovit z problémů s dostupností, které mají vliv na oblast, která je hostitelem vaší API Management služby, můžete kdykoli znovu vytvořit službu v jiné oblasti. V závislosti na čase vaší doby obnovení můžete chtít ponechat pohotovostní službu v jedné nebo několika oblastech. Můžete se také pokusit zachovat synchronizaci konfigurace a obsahu s aktivní službou podle vašeho cíle bodu obnovení. Funkce zálohování a obnovení služby poskytují potřebné stavební bloky pro implementaci strategie zotavení po havárii.

Operace zálohování a obnovení se dají použít i pro replikaci API Managementch konfigurací služby mezi provozními prostředími, třeba při vývoji a přípravě. Upozorňujeme, že se zkopírují i běhová data, jako jsou uživatelé a odběry, což nemusí být vždy žádoucí.

V této příručce se dozvíte, jak automatizovat operace zálohování a obnovení a jak zajistit úspěšné ověření požadavků na zálohování a obnovení Azure Resource Manager.

> [!IMPORTANT]
> Operace obnovení nemění konfiguraci vlastního názvu hostitele cílové služby. Pro aktivní i pohotovostní služby doporučujeme použít stejný vlastní název hostitele a certifikát TLS, takže po dokončení operace obnovení bude možné provoz znovu směrovat do pohotovostní instance pomocí jednoduché změny DNS CNAME.
>
> Operace zálohování nezachycuje předem agregovaná data protokolu používaná v sestavách zobrazených v okně Analýza v Azure Portal.

> [!WARNING]
> Platnost každé zálohy vyprší po 30 dnech. Pokud se pokusíte obnovit zálohu po uplynutí 30denní doby platnosti, obnovení se nezdaří a zobrazí se zpráva `Cannot restore: backup expired`.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="authenticating-azure-resource-manager-requests"></a>Ověřování žádostí Azure Resource Manager

> [!IMPORTANT]
> REST API pro zálohování a obnovení používá Azure Resource Manager a má jiný ověřovací mechanismus než rozhraní REST API ke správě entit API Management. Postup v této části popisuje, jak ověřit požadavky Azure Resource Manager. Další informace najdete v tématu [ověřování žádostí Azure Resource Manager](/rest/api/index).

Všechny úlohy, které provedete v prostředcích pomocí Azure Resource Manager, musí být ověřeny pomocí Azure Active Directory pomocí následujících kroků:

-   Přidejte aplikaci do tenanta Azure Active Directory.
-   Nastavte oprávnění pro aplikaci, kterou jste přidali.
-   Získejte token pro ověřování požadavků Azure Resource Manager.

### <a name="create-an-azure-active-directory-application"></a>Vytvoření aplikace Azure Active Directory

1. Přihlaste se k [Azure Portal](https://portal.azure.com).
2. Pomocí předplatného, které obsahuje vaši instanci služby API Management, přejděte v **Azure Active Directory** na kartu **Registrace aplikací** (Azure Active Directory > spravovat/registrace aplikací).

    > [!NOTE]
    > Pokud Azure Active Directory výchozí adresář není pro váš účet viditelný, kontaktujte správce předplatného Azure a udělte vašemu účtu požadovaná oprávnění.

3. Klikněte na **Registrace nové aplikace**.

    Na pravé straně se zobrazí okno **vytvořit** . To je místo, kam zadáte relevantní informace o aplikaci AAD.

4. Zadejte název aplikace.
5. Jako typ aplikace vyberte **nativní**.
6. Zadejte adresu URL zástupného symbolu, například `http://resources` pro **identifikátor URI přesměrování**, protože se jedná o požadované pole, ale hodnota se nepoužije později. Kliknutím na zaškrtávací políčko aplikaci uložíte.
7. Klikněte na **vytvořit**.

### <a name="add-an-application"></a>Přidání aplikace

1. Po vytvoření aplikace klikněte na **Nastavení**.
2. Klikněte na **požadovaná oprávnění**.
3. Klikněte na **+ Přidat**.
4. Stiskněte **Vybrat rozhraní API**.
5. Vyberte **Windows** **Azure rozhraní API pro správu služeb**.
6. Stiskněte **Vybrat**.

    ![Přidání oprávnění](./media/api-management-howto-disaster-recovery-backup-restore/add-app.png)

7. Klikněte na **delegovaná oprávnění** vedle nově přidané aplikace, zaškrtněte políčko pro **přístup ke službě Azure Service Management (Preview)** .
8. Stiskněte **Vybrat**.
9. Klikněte na **udělit oprávnění**.

### <a name="configuring-your-app"></a>Konfigurace aplikace

Před voláním rozhraní API, která generují zálohu a obnoví je, potřebujete získat token. Následující příklad používá balíček NuGet [Microsoft. IdentityModel. clients. Active](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) pro načtení tokenu.

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

Pomocí následujících pokynů nahraďte `{tenant id}`, `{application id}` a `{redirect uri}`:

1. Hodnotu `{tenant id}` nahraďte ID klienta vytvořeného Azure Active Directory aplikace. Kliknutím na **Registrace aplikací** **koncových bodů** ->  získáte přístup k ID.

    ![Koncové body][api-management-endpoint]

2. Nahraďte `{application id}` hodnotou, kterou získáte, přechodem na stránku **Nastavení** .
3. Hodnotu `{redirect uri}` nahraďte hodnotou z karty **identifikátory URI pro přesměrování** vaší aplikace Azure Active Directory.

    Po zadání hodnot by měl příklad kódu vracet token podobný následujícímu příkladu:

    ![Klíčové][api-management-arm-token]

    > [!NOTE]
    > Platnost tokenu může po určitou dobu vypršet. Spusťte znovu ukázku kódu pro vygenerování nového tokenu.

## <a name="calling-the-backup-and-restore-operations"></a>Volání operací zálohování a obnovení

Rozhraní REST API jsou [služby API Management – Backup](/rest/api/apimanagement/2019-01-01/apimanagementservice/backup) a [Služba API Management – obnovení](/rest/api/apimanagement/2019-01-01/apimanagementservice/restore).

Před voláním operací zálohování a obnovení, které jsou popsány v následujících částech, nastavte hlavičku autorizační žádosti pro vaše volání REST.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="step1"> </a>Zálohování služby API Management

Pokud chcete zálohovat službu API Management, vydejte následující požadavek HTTP:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

kde:

-   `subscriptionId`-ID předplatného, které má API Management službu, kterou se pokoušíte zálohovat
-   `resourceGroupName`-název skupiny prostředků služby Azure API Management
-   `serviceName` – název služby API Management, kterou provádíte zálohu zadaného v době jejího vytvoření.
-   `api-version` – nahradit `2018-06-01-preview`

V těle žádosti zadejte název cílového účtu Azure Storage, přístupový klíč, název kontejneru objektů BLOB a název zálohy:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Nastavte hodnotu v hlavičce požadavku `Content-Type` na `application/json`.

Zálohování je dlouhodobá operace, která může trvat déle než minutu. Pokud byla žádost úspěšná a začala proces zálohování, obdržíte kód stavu odpovědi `202 Accepted` s hlavičkou `Location`. Pokud chcete zjistit stav operace, vytvořte na adrese URL v hlavičce `Location` požadavky GET. I když probíhá zálohování, budete dál dostávat stavový kód 202. Kód odpovědi `200 OK` označuje úspěšné dokončení operace zálohování.

Při vytváření žádosti o zálohu Pamatujte na následující omezení:

-   **Kontejner** zadaný v těle požadavku **musí existovat**.
-   Zatímco probíhá zálohování, **Vyhněte se změnám v řízení služeb** , jako je například upgrade SKU nebo downgrading, změna v názvu domény a další.
-   Obnovení **zálohy je zaručeno pouze po dobu 30 dnů** od okamžiku jejího vytvoření.
-   **Data o využití** používaná pro vytváření sestav Analytics **nejsou součástí** zálohy. Pomocí služby [Azure API Management REST API][azure api management rest api] pravidelně načítat analytické sestavy pro bezpečné používání.
-   Kromě toho následující položky nejsou součástí zálohovaných dat: vlastní certifikáty SSL a všechny zprostředkující nebo kořenové certifikáty nahrané zákazníkem, obsahem portálu pro vývojáře a nastavením integrace virtuální sítě.
-   Frekvence, se kterou provádíte zálohování služby, má vliv na cíl bodu obnovení. Pro minimalizaci doporučujeme, abyste implementovali pravidelné zálohování a prováděli zálohování na vyžádání po provedení změn ve službě API Management.
-   **Změny** v konfiguraci služby, (například rozhraní API, zásady a vzhled portálu pro vývojáře) během procesu zálohování, **mohou být vyloučeny ze zálohy a budou ztraceny**.
-   **Povolí** přístup z řídicí roviny na účet Azure Storage. Zákazník musí na svém účtu úložiště otevřít následující sadu příchozích IP adres pro zálohování. 
    > 13.84.189.17/32, 13.85.22.63/32, 23.96.224.175/32, 23.101.166.38/32, 52.162.110.80/32, 104.214.19.224/32, 13.64.39.16/32, 40.81.47.216/32, 51.145.179.78/32, 52.142.95.35/32, 40.90.185.46/32, 20.40.125.155/32
### <a name="step2"> </a>Obnovení služby API Management

Chcete-li obnovit službu API Management z dříve vytvořené zálohy, proveďte následující požadavek HTTP:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

kde:

-   `subscriptionId`-ID předplatného, které obsahuje službu API Management, do které obnovujete zálohu
-   `resourceGroupName`-název skupiny prostředků, která obsahuje službu Azure API Management, do které provádíte obnovení zálohy
-   `serviceName` – název služby API Management, která se obnovuje v době jejího vytvoření.
-   `api-version` – nahradit `2018-06-01-preview`

V těle žádosti zadejte umístění záložního souboru. To znamená, že přidejte název účtu Azure Storage, přístupový klíč, název kontejneru objektů BLOB a název zálohy:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Nastavte hodnotu v hlavičce požadavku `Content-Type` na `application/json`.

Obnovení je dlouhodobá operace, která může trvat až 30 minut, než se dokončí. Pokud se žádost úspěšně dokončila a proces obnovení začal, obdržíte kód stavu odpovědi `202 Accepted` s hlavičkou `Location`. Pokud chcete zjistit stav operace, vytvořte na adrese URL v hlavičce `Location` požadavky GET. I když probíhá obnovení, budete mít i nadále přijatý stavový kód 202. Kód odpovědi `200 OK` označuje úspěšné dokončení operace obnovení.

> [!IMPORTANT]
> **SKU** služby, která se má obnovit do, se **musí shodovat** s SKU obnovované zálohované služby.
>
> **Změny** v konfiguraci služby (například rozhraní API, zásady, vzhled portálu pro vývojáře), zatímco probíhá operace obnovení, **mohou být přepsány**.

<!-- Dummy comment added to suppress markdown lint warning -->

> [!NOTE]
> Operace zálohování a obnovení se dají provádět taky pomocí příkazů PowerShell [_Backup-AzApiManagement_](/powershell/module/az.apimanagement/backup-azapimanagement) a [_Restore-AzApiManagement_](/powershell/module/az.apimanagement/restore-azapimanagement) .

## <a name="next-steps"></a>Další kroky

V různých návodech k procesu zálohování a obnovení si Projděte následující zdroje informací.

-   [Replikace účtů Azure API Management](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
-   [Automatizace API Management zálohování a obnovování pomocí Logic Apps](https://github.com/Azure/api-management-samples/tree/master/tutorials/automating-apim-backup-restore-with-logic-apps)
-   [Azure API Management: zálohování a obnovení konfigurace](https://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
    _Metoda Stanislav se neshoduje s oficiálními pokyny, ale je zajímavá._

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
