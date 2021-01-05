---
title: Ověření aplikace pro přístup ke službě Azure Signal
description: Tento článek poskytuje informace o ověřování aplikace s Azure Active Directory pro přístup ke službě Azure Signal.
author: terencefan
ms.author: tefa
ms.service: signalr
ms.topic: conceptual
ms.date: 08/03/2020
ms.openlocfilehash: 97386b18360e22b457dbcdda53c4f81e7d4ed272
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2020
ms.locfileid: "97797483"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-azure-signalr-service"></a>Ověření aplikace s Azure Active Directory pro přístup ke službě Azure Signal
Microsoft Azure poskytuje integrované řízení přístupu pro prostředky a aplikace založené na Azure Active Directory (Azure AD). Klíčovou výhodou použití Azure AD se službou Azure Signaler je, že nemusíte ukládat přihlašovací údaje do kódu již. Místo toho můžete požádat o přístupový token OAuth 2,0 z platformy Microsoft Identity Platform. Název prostředku pro vyžádání tokenu `https://signalr.azure.com/` . Azure AD ověřuje objekt zabezpečení (aplikaci, skupinu prostředků nebo instanční objekt), který spouští aplikaci. Pokud je ověření úspěšné, služba Azure AD vrátí přístupový token do aplikace a aplikace pak může pomocí přístupového tokenu autorizovat požadavky na prostředky služby signalizace Azure.

Když je role přiřazená k objektu zabezpečení Azure AD, poskytuje Azure přístup k těmto prostředkům pro daný objekt zabezpečení. Přístup může být vymezený na úrovni předplatného, skupiny prostředků nebo prostředku nástroje Azure Signal. Zabezpečení Azure AD může přiřadit role uživateli, skupině, instančnímu objektu služby nebo [spravované identitě pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md). 

> [!NOTE]
> Definice role je kolekce oprávnění. Řízení přístupu na základě role (RBAC) řídí způsob, jakým se tato oprávnění uplatňují prostřednictvím přiřazení role. Přiřazení role se skládá ze tří prvků: objekt zabezpečení, definice role a obor. Další informace najdete v tématu [porozumění různým rolím](../role-based-access-control/overview.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Registrace aplikace pomocí tenanta Azure AD
Prvním krokem při použití Azure AD k autorizaci prostředků služby signalizace Azure je registrace aplikace pomocí tenanta Azure AD z [Azure Portal](https://portal.azure.com/). Při registraci aplikace zadáváte informace o aplikaci službě AD. Azure AD pak poskytuje ID klienta (označované také jako ID aplikace), které můžete použít k přidružení aplikace k modulu runtime služby Azure AD. Další informace o ID klienta najdete [v tématu aplikace a objekty zabezpečení služby v Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). 

Následující obrázky ukazují kroky pro registraci webové aplikace:

![Registrace aplikace](./media/authenticate/app-registrations-register.png)

> [!Note]
> Pokud aplikaci zaregistrujete jako nativní aplikaci, můžete pro identifikátor URI přesměrování zadat libovolný platný identifikátor URI. Pro nativní aplikace nemusí být tato hodnota skutečnou adresou URL. Pro webové aplikace musí být identifikátor URI pro přesměrování platným identifikátorem URI, protože určuje adresu URL, na které jsou tokeny poskytovány.

Po zaregistrování aplikace se v části **Nastavení** zobrazí **ID aplikace (klienta)** :

![ID aplikace registrované aplikace](./media/authenticate/application-id.png)

Další informace o registraci aplikace v Azure AD najdete v tématu [integrování aplikací pomocí Azure Active Directory](../active-directory/develop/quickstart-register-app.md).


### <a name="create-a-client-secret"></a>Vytvoření tajného klíče klienta   
Aplikace potřebuje při žádosti o tokenu klíč klienta k prokázání jeho identity. Chcete-li přidat tajný klíč klienta, postupujte podle těchto kroků.

1. Přejděte k registraci vaší aplikace v Azure Portal.
1. Vyberte nastavení **certifikáty & tajných** kódů.
1. V části **tajné klíče klienta** vyberte **nový tajný klíč klienta** a vytvořte nový tajný klíč.
1. Zadejte popis tajného kódu a vyberte požadovaný interval vypršení platnosti.
1. Hodnotu nového tajného klíče hned zkopírujte do zabezpečeného umístění. Hodnota Fill se zobrazí pouze jednou.

![Vytvoření tajného klíče klienta](./media/authenticate/client-secret.png)

### <a name="upload-a-certificate"></a>Odeslat certifikát

Místo vytvoření tajného klíče klienta můžete také odeslat certifikaci.

![Odeslat certifikaci](./media/authenticate/certification.png)

## <a name="add-rbac-roles-using-the-azure-portal"></a>Přidání rolí RBAC pomocí Azure Portal  
Další informace o správě přístupu k prostředkům Azure pomocí RBAC a Azure Portal najdete v [tomto článku](..//role-based-access-control/role-assignments-portal.md). 

Po určení vhodného oboru pro přiřazení role přejděte k tomuto prostředku v Azure Portal. Zobrazit nastavení řízení přístupu (IAM) pro prostředek a podle těchto pokynů můžete spravovat přiřazení rolí:

1. V [Azure Portal](https://portal.azure.com/)přejděte na prostředek signalizace.
1. Vyberte **Access Control (IAM)** a zobrazte nastavení řízení přístupu pro službu Azure Signal. 
1. Vyberte kartu **přiřazení rolí** a zobrazte seznam přiřazení rolí. Na panelu nástrojů vyberte tlačítko **Přidat** a pak vyberte **Přidat přiřazení role**. 

    ![Přidat tlačítko na panelu nástrojů](./media/authenticate/role-assignments-add-button.png)

1. Na stránce **Přidat přiřazení role** proveďte následující kroky:
    1. Vyberte **roli nástroje Azure Signal** , kterou chcete přiřadit. 
    1. Vyhledejte **objekt zabezpečení** (uživatel, skupina, instanční objekt), ke kterému chcete přiřadit roli.
    1. Vyberte **Uložit** a uložte přiřazení role. 

        ![Přiřazení role k aplikaci](./media/authenticate/assign-role-to-application.png)

    1. Identita, ke které jste přiřadili roli, se zobrazí v seznamu v rámci této role. Například na následujícím obrázku vidíte, že aplikace `signalr-dev` a `signalr-service` jsou v roli serveru aplikace signaler. 
        
        ![Seznam přiřazení rolí](./media/authenticate/role-assignment-list.png)

Podle podobných kroků můžete přiřadit obor role ke skupině prostředků nebo předplatnému. Po definování role a jejího oboru můžete toto chování otestovat pomocí ukázek [v tomto umístění GitHubu](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac).

## <a name="sample-codes-while-configuring-your-app-server"></a>Ukázkové kódy při konfiguraci serveru aplikace

Přidejte následující možnosti, když `AddAzureSignalR` :

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientSecret=<clientSecret>;tenantId=<tenantId>";
});
```

Nebo stačí nakonfigurovat `ConnectionString` v `appsettings.json` souboru.

```json
{
"Azure": {
    "SignalR": {
      "Enabled": true,
      "ConnectionString": "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientSecret=<clientSecret>;tenantId=<tenantId>"
    }
  },
}
```

Pokud používáte `certificate` , změňte na `clientSecret` takto `clientCert` :

```C#
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientCert=<clientCertFilepath>;tenantId=<tenantId>";
```

## <a name="next-steps"></a>Další kroky
- Další informace o RBAC najdete v tématu [co je řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/overview.md)?
- Další informace o tom, jak přiřadit a spravovat přiřazení rolí Azure pomocí Azure PowerShell, Azure CLI nebo REST API, najdete v těchto článcích:
    - [Správa řízení přístupu na základě role (RBAC) pomocí Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Správa řízení přístupu na základě role (RBAC) pomocí Azure CLI](../role-based-access-control/role-assignments-cli.md)
    - [Správa řízení přístupu na základě role (RBAC) pomocí REST API](../role-based-access-control/role-assignments-rest.md)
    - [Správa řízení přístupu na základě role (RBAC) pomocí šablon Azure Resource Manager](../role-based-access-control/role-assignments-template.md)

Podívejte se na následující související články:
- [Ověření spravované identity pomocí Azure Active Directory pro přístup ke službě Azure Signal](authenticate-managed-identity.md)
- [Autorizace přístupu ke službě Azure Signaler pomocí Azure Active Directory](authorize-access-azure-active-directory.md)