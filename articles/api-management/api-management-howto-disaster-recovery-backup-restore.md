---
title: Pomocí zotavení po havárii implementovat zálohování a obnovení ve službě Azure API Management | Dokumentace Microsoftu
description: Zjistěte, jak použít zálohování a obnovení provést zotavení po havárii ve službě Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: apimpm
ms.openlocfilehash: 0dc7e8836f1e6a11c44f5e0f337015cac53a92d4
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51252798"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Implementace zotavení po havárii pomocí služby zálohování a obnovení ve službě Azure API Management

Výběrem publikovat a spravovat vaše rozhraní API pomocí Azure API Management se využívat mnoho funkcí infrastruktury s odolností, které by jinak bylo návrhu, implementaci a správě a odolnost proti chybám. Platforma Azure omezuje velká část potenciálních selhání za zlomek nákladů.

Pokud chcete obnovit z problémů s dostupností by to ovlivnilo oblasti, kde se hostuje vaše služba API Management, by měl být připraven k rekonstrukci služby v jiné oblasti kdykoli. V závislosti na dostupnosti cíle a plánovaná doba obnovení můžete chtít rezervovat zálohování služby v jedné nebo několika oblastech a pokuste se udržovat synchronizované se službou active jejich konfigurace a obsahu. Funkce služby "zálohování a obnovení" poskytuje nezbytné stavební blok pro implementaci strategie zotavení po havárii.

Tato příručka ukazuje, jak k ověřování požadavků Azure Resource Manageru a jak zálohovat a obnovovat vaše instance služby API Management.

> [!NOTE]
> Proces zálohování a obnovení instance služby API Management pro zotavení po havárii lze použít také pro replikaci instance služby API Management pro scénáře, jako je pracovní.
>
> Každá záloha vyprší po 30 dnech. Pokud se pokusíte obnovit zálohu po vypršení doby platnosti 30 dnů, obnovení se nezdaří s `Cannot restore: backup expired` zprávy.
>
>

## <a name="authenticating-azure-resource-manager-requests"></a>Požadavky na ověřování Azure Resource Manageru

> [!IMPORTANT]
> Rozhraní REST API pro zálohování a obnovení pomocí Azure Resource Manageru a má jiný mechanismus ověřování než rozhraní REST API pro správu entit služby API Management. Kroky v této části popisují, jak ověřování požadavků Azure Resource Manageru. Další informace najdete v tématu [požadavků ověřování Azure Resource Manageru](https://msdn.microsoft.com/library/azure/dn790557.aspx).

Všechny úlohy, které můžete provést na prostředky pomocí Azure Resource Manageru, musí být ověřené na Azure Active Directory pomocí následujících kroků:

* Přidání aplikace do tenanta Azure Active Directory.
* Nastavit oprávnění pro aplikaci, kterou jste přidali.
* Získání tokenu pro ověření požadavků na Azure Resource Manager.

### <a name="create-an-azure-active-directory-application"></a>Vytvoření aplikace Azure Active Directory

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 
2. Pomocí předplatné obsahující instance služby API Management, přejděte do **registrace aplikací** kartu **Azure Active Directory** (Azure Active Directory > Registrace spravovat/aplikací).

    > [!NOTE]
    > Pokud výchozí adresář Azure Active Directory není viditelné pro váš účet, obraťte se na správce předplatného Azure udělit požadovaná oprávnění k vašemu účtu.
3. Klikněte na **Registrace nové aplikace**.

    **Vytvořit** okna se zobrazí na pravé straně. Je to, kde zadejte příslušné informace aplikaci AAD.
4. Zadejte název aplikace.
5. Typ aplikace vyberte **nativní**.
6. Zadejte adresu URL zástupného symbolu, jako `http://resources` pro **identifikátor URI pro přesměrování**, jako je povinné pole, ale hodnoty se později nepoužívá. Klikněte na zaškrtávací políčko a uložte aplikaci.
7. Klikněte na možnost **Vytvořit**.

### <a name="add-an-application"></a>Přidání aplikace

1. Po vytvoření aplikace klikněte na tlačítko **nastavení**.
2. Klikněte na tlačítko **požadovaná oprávnění**.
3. Klikněte na tlačítko **+ přidat**.
4. Stisknutím klávesy **vyberte rozhraní API**.
5. Zvolte **Windows** **Azure Service Management API**.
6. Stisknutím klávesy **vyberte**. 

    ![Přidání oprávnění](./media/api-management-howto-disaster-recovery-backup-restore/add-app.png)

7. Klikněte na tlačítko **delegovaná oprávnění** vedle nově přidané aplikace zaškrtněte políčko u **přístup ke správě služeb Azure (preview)**.
8. Stisknutím klávesy **vyberte**.
9. Klikněte na tlačítko **udělit oprávnění**.

### <a name="configuring-your-app"></a>Konfigurace aplikace

Před volání rozhraní API, která generovat zálohování a obnovení, je potřeba získat token. V následujícím příkladu [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) balíčku NuGet se načíst token.

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

Nahraďte `{tentand id}`, `{application id}`, a `{redirect uri}` pomocí následujících pokynů:

1. Nahraďte `{tenant id}` s id tenanta Azure Active Directory aplikaci, kterou jste vytvořili. Id se zpřístupní po kliknutí **registrace aplikací** -> **koncové body**.

    ![Koncové body][api-management-endpoint]
2. Nahraďte `{application id}` s hodnotou, získáte přechodem na **nastavení** stránky.
3. Nahradit `{redirect uri}` s hodnotou od **identifikátory URI přesměrování** kartu aplikace Azure Active Directory.

    Jakmile jsou hodnoty zadané, příklad kódu by měla vrátit token podobně jako v následujícím příkladu:

    ![Podpisový][api-management-arm-token]

    > [!NOTE]
    > Tokenu může vypršet jejich platnost po určité době. Spuštění vzorového kódu znovu vygenerovat nový token.

## <a name="calling-the-backup-and-restore-operations"></a>Volání operace zálohování a obnovení

Rozhraní REST API jsou [služby Api Management – zálohování](https://docs.microsoft.com/rest/api/apimanagement/apimanagementservice/apimanagementservice_backup) a [služby Api Management – obnovení](https://docs.microsoft.com/rest/api/apimanagement/apimanagementservice/apimanagementservice_restore).

Před voláním operace "zálohování a obnovení", které jsou popsané v následujících částech nastavte autorizační hlavičky žádosti pro volání REST.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="step1"> </a>Zálohování služby API Management
Pro zálohování problém se službou API Management následující požadavek protokolu HTTP:

```
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

kde:

* `subscriptionId` – id předplatného, které obsahuje službu API Management, kterou se pokoušíte zálohování
* `resourceGroupName` – Název skupiny prostředků služby Azure API Management
* `serviceName` – název služby API Management vytvoříte zálohu určit v okamžiku svého vytvoření
* `api-version` -nahradit `2018-06-01-preview`

V textu požadavku zadejte název cílového účtu úložiště Azure, přístupový klíč, název kontejneru objektů blob a název zálohy:


```json
{
  "storageAccount": "{storage account name for the backup}",
  "accessKey": "{access key for the account}",
  "containerName": "{backup container name}",
  "backupName": "{backup blob name}"
}
```

Nastavte hodnotu `Content-Type` hlavičku požadavku na `application/json`.

Zálohování je dlouho běžící operace, která může trvat několik minut.  Pokud byl zahájen proces zálohování požadavek byl úspěšný, zobrazí se `202 Accepted` stavový kód odpovědi s `Location` záhlaví.  Ujistěte se, získejte požadavků na adresu URL v `Location` záhlaví pro zjištění stavu operace. Zatímco probíhá zálohování, můžete nadále přijímat '202 přijato' stavový kód. Kód odpovědi `200 OK` označuje úspěšné dokončení operace zálohování.

Při zálohování požadavku, mějte na paměti následující omezení.

* **Kontejner** zadaná v textu požadavku **musí existovat**.
* Probíhá zálohování můžete **by se neměly pokoušet žádné operace správy služby** například upgrade skladové položky nebo přechod na starší verzi, změnu názvu domény, atd.
* Obnovení **zálohování je zaručeno, že pouze po dobu 30 dnů** od okamžiku svého vytvoření.
* **Data o využití** použitý k vytvoření sestavy analýzy **nezahrnuje** v záloze. Použití [REST API služby Azure API Management] [ Azure API Management REST API] pravidelně načíst analytických sestav z bezpečnostních důvodů udržován.
* Frekvence, se kterým můžete provést zálohování služby vliv na vaše cíle bodu obnovení. Chcete-li minimalizovat, doporučení je provádění pravidelného zálohování i zálohování na vyžádání po důležitých změnách služby API Management.
* **Změny** provedené v konfiguraci služby (například rozhraní API, zásady, vzhledu portálu pro vývojáře) během zálohování probíhá operace **nemusí být zahrnuté do zálohování a proto budou ztraceny**.

### <a name="step2"> </a>Obnovení služby API Management
Chcete-li obnovit API Management service z dříve vytvořeného odesílat následující požadavky HTTP:

```
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

kde:

* `subscriptionId` – id předplatného, které obsahuje obnovujete zálohu do služby API Management
* `resourceGroupName` – Název skupiny prostředků obsahující obnovujete zálohu do služby Azure API Management
* `serviceName` – název služby API Management service, který se má obnovit do zadané v okamžiku svého vytvoření
* `api-version` -nahradit `2018-06-01-preview`

V textu požadavku zadejte umístění záložního souboru, který je, název účtu služby Azure storage, přístupový klíč, název kontejneru objektů blob a název zálohy:

```json
{
  "storageAccount": "{storage account name for the backup}",
  "accessKey": "{access key for the account}",
  "containerName": "{backup container name}",
  "backupName": "{backup blob name}"
}
```

Nastavte hodnotu `Content-Type` hlavičku požadavku na `application/json`.

Obnovení je dlouho běžící operace, která může trvat až 30 nebo více minut na dokončení. Pokud byl zahájen proces obnovení požadavek byl úspěšný, zobrazí se `202 Accepted` stavový kód odpovědi s `Location` záhlaví. Ujistěte se, získejte požadavků na adresu URL v `Location` záhlaví pro zjištění stavu operace. Když obnovení probíhá, nadále zobrazí '202 přijato' stavový kód. Kód odpovědi `200 OK` označuje úspěšné dokončení operace obnovení.

> [!IMPORTANT]
> **SKU** služby, který se má obnovit do **musí odpovídat** skladovou Položku zálohovanou služba obnovena.
>
> **Změny** provedené v konfiguraci služby (například rozhraní API, zásady, vzhledu portálu pro vývojáře) při obnovení se probíhající operace **může dojít k přepsání**.

> [!NOTE]
> Operace zálohování a obnovení lze provést také pomocí Powershellu *Backup-AzureRmApiManagement* a *Restore-AzureRmApiManagement* příkazy v uvedeném pořadí.

## <a name="next-steps"></a>Další postup

Projděte si následující zdroje pro různé návody procesu zálohování a obnovení.

* [Replikovat účty Azure API Management](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
* [Automatizace rozhraní API správy zálohování a obnovení pomocí Logic Apps](https://github.com/Azure/api-management-samples/tree/master/tutorials/automating-apim-backup-restore-with-logic-apps)
* [Azure API Management: Zálohování a obnovení konfigurace](https://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
  *přístup podrobné podle Stuart neodpovídá oficiální pokyny, ale je zajímavé.*

[Backup an API Management service]: #step1
[Restore an API Management service]: #step2

[Azure API Management REST API]: http://msdn.microsoft.com/library/azure/dn781421.aspx

[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png

[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
