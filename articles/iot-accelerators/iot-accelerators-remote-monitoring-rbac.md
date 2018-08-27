---
title: Řízení přístupu vzdálené monitorování – Azure | Dokumentace Microsoftu
description: Tento článek obsahuje informace o konfiguraci řízení přístupu na základě rolí (RBAC) v akcelerátoru řešení vzdáleného monitorování
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/06/2018
ms.topic: conceptual
ms.openlocfilehash: 956cb80ddbf96f23585dd52f3dc1013c7a665113
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42886306"
---
# <a name="configure-role-based-access-controls-in-the-remote-monitoring-solution-accelerator"></a>Konfigurace řízení přístupu na základě rolí v akcelerátoru řešení vzdáleného monitorování

Tento článek obsahuje informace o tom, jak nakonfigurovat řízení přístupu na základě rolí v akcelerátoru řešení vzdáleného monitorování. Řízení přístupu na základě rolí umožňuje omezit přístup pro jednotlivé uživatele na konkrétní funkce v řešení.

## <a name="default-settings"></a>Výchozí nastavení

Při prvním nasazení řešení vzdáleného monitorování, existují dvě role: **správce** a **jen pro čtení**.

Žádný uživatel ve **správce** role má úplný přístup k řešení. Uživatel v **jen pro čtení** role nemůže provádět žádnou z následujících úloh:

- Aktualizovat upozornění
- Odstranit upozornění
- Vytvoření zařízení
- Aktualizace zařízení
- Odstranění zařízení
- Vytvoření skupin zařízení
- Aktualizace skupiny zařízení
- Odstranění skupiny zařízení
- Vytvoření pravidel
- Aktualizace pravidla
- Odstranit pravidla
- Vytváření úloh
- Správa aktualizací SIM

Osoba, která nasazuje řešení vzdáleného monitorování se automaticky přiřadí **správce** role a je vlastníka aplikace Azure Active Directory. Jako vlastníka aplikace můžete přiřadit role jiným uživatelům na portálu Azure portal.

Pokud chcete přiřadit role v řešení jiného uživatele, musí se také nastavit jako vlastníka aplikace na webu Azure Portal.

## <a name="add-or-remove-users"></a>Přidání nebo odebrání uživatelů

Pomocí webu Azure portal přidat nebo odebrat uživatele z řešení vzdáleného monitorování. Následující kroky použijte [podniková aplikace Azure Active Directory](../active-directory/manage-apps/add-application-portal.md#find-your-azure-ad-tenant-application) , který se pro vás vytvořil při nasazení řešení vzdáleného monitorování.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Zkontrolujte, [je uživatel v adresáři](../active-directory/fundamentals/add-users-azure-active-directory.md) používáte. Jste zvolili pro použití při přihlášení k adresáři [akcelerátory řešení IoT Microsoft Azure](https://www.azureiotsolutions.com/Accelerators) lokality. Název adresáře je viditelný v pravém horním rohu [stránky](https://www.azureiotsolutions.com/Accelerators).

1. Najít **podniková aplikace** pro vaše řešení na webu Azure Portal. Jednou, seznam můžete filtrovat podle nastavení **typ aplikace** k **všechny aplikace**. Vyhledejte název vaší aplikace pomocí aplikace. Název aplikace je název vašeho řešení vzdáleného monitorování. Na následujícím snímku obrazovky zobrazované názvy řešení a aplikace jsou **contoso-rm4**.

    ![Podniková aplikace](media/iot-accelerators-remote-monitoring-rbac/appregistration.png)

1. Zkontrolujte jste vlastníkem aplikace tak, že kliknete na aplikaci a pak levým na **vlastníky**. Na následujícím snímku obrazovky **správce společnosti Contoso** je vlastníkem **contoso-rm4** aplikace:

    ![Vlastníci](media/iot-accelerators-remote-monitoring-rbac/owners.png)

    Pokud nejste vlastníkem, budete muset požádat stávající vlastník, aby vás přidal do seznamu. Pouze vlastníci přiřazení aplikačních rolí, jako **správce** nebo **jen pro čtení** ostatním uživatelům.

1. Pokud chcete zobrazit seznam uživatelů, které jsou přiřazené k rolím v aplikaci, klikněte na tlačítko **uživatelů a skupin**.

1. Chcete-li přidat uživatele, klikněte na tlačítko **+ přidat uživatele**a potom klikněte na tlačítko **uživatele a skupiny, není vybrán žádný** vybrat uživatele z adresáře.

1. Přiřadit uživatele k roli, klikněte na tlačítko **vybrat roli Nevybrán žádný** a zvolte buď **správce** nebo **jen pro čtení** roli pro uživatele. Klikněte na tlačítko **vyberte**a potom klikněte na tlačítko **přiřadit**.

    ![Výběr role](media/iot-accelerators-remote-monitoring-rbac/selectrole.png)

1. Uživatel teď přístup k řešení vzdáleného monitorování s oprávněními definovanými rolí.

1. Můžete odstranit uživatele z aplikace na **uživatelů a skupin** stránky na portálu.

## <a name="create-a-custom-role"></a>Vytvoření vlastní role

Řešení vzdáleného monitorování obsahuje **správce** a **jen pro čtení** role při prvním nasazení. Můžete přidat vlastní role s jinou sadu oprávnění. Pokud chcete definovat vlastní roli, budete muset:

- Přidáte novou roli do aplikace na webu Azure Portal.
- Definujte zásady pro novou roli v mikroslužbě ověřování a autorizace.
- Aktualizace řešení webového uživatelského rozhraní.

### <a name="define-a-custom-role-in-the-azure-portal"></a>Definovat vlastní roli na portálu Azure portal

Následující kroky popisují, jak přidat roli do aplikace v Azure Active Directory. V tomto příkladu vytvoříte novou roli, která umožňuje členům vytvářet, aktualizovat a odstraňovat zařízení do řešení vzdáleného monitorování.

1. Najít **registrace aplikace** pro vaše řešení na webu Azure Portal. Název aplikace je název vašeho řešení vzdáleného monitorování. Na následujícím snímku obrazovky zobrazované názvy řešení a aplikace jsou **contoso-rm4**.

    ![Registrace aplikace](media/iot-accelerators-remote-monitoring-rbac/appregistration2.png)

1. Vyberte svou aplikaci a pak klikněte na tlačítko **Manifest**. Zobrazí se dva existující [role aplikace](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles) definované pro aplikaci:

    ![Zobrazení manifestu](media/iot-accelerators-remote-monitoring-rbac/viewmanifest.png)

1. Upravit manifest pro přidat roli s názvem **ManageDevices** jak je znázorněno v následujícím fragmentu kódu. Budete potřebovat jedinečný řetězec jako identifikátor GUID pro nové ID role. Můžete vygenerovat nový identifikátor GUID pomocí služby, jako [Online GUID Generator](https://www.guidgenerator.com/):

    ```json
    "appRoles": [
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "Admin",
        "id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
        "isEnabled": true,
        "description": "Administrator access to the application",
        "value": "Admin"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "Read Only",
        "id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
        "isEnabled": true,
        "description": "Read only access to device information",
        "value": "ReadOnly"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "ManageDevices",
        "id": "ADD A NEW GUID HERE",
        "isEnabled": true,
        "description": "A new role for the application.",
        "value": "ManageDevices"
      }
    ],
    ```

    Uložte změny.

### <a name="define-a-policy-for-the-new-role"></a>Definovat zásady pro novou roli

Po přidání role do aplikace na webu Azure Portal, je třeba definovat zásady v [roles.json](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/pcs-auth/Services/data/policies/roles.json) pro roli, která se přiřadí oprávnění potřebná ke správě zařízení.

1. Klonování [ověřování a autorizace mikroslužeb](https://github.com/Azure/pcs-auth-dotnet) úložiště z Githubu do místního počítače.

1. Upravit **Services/data/policies/roles.json** soubor a přidejte zásadu **ManageDevices** role, jak je znázorněno v následujícím fragmentu kódu. **ID** a **Role** hodnoty musí odpovídat definici role v manifestu aplikace z předchozí části. Seznam povolených akcí umožňuje někdo v **ManageDevices** role vytvářet, aktualizovat a odstraňovat zařízení připojených k řešení:

    ```json
    {
    "Items": [
      {
        "Id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
        "Role": "admin",
        "AllowedActions": [
          "UpdateAlarms",
          "DeleteAlarms",
          "CreateDevices",
          "UpdateDevices",
          "DeleteDevices",
          "CreateDeviceGroups",
          "UpdateDeviceGroups",
          "DeleteDeviceGroups",
          "CreateRules",
          "UpdateRules",
          "DeleteRules",
          "CreateJobs",
          "UpdateSimManagement"
        ]
      },
      {
        "Id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
        "Role": "readOnly",
        "AllowedActions": []
      },
      {
        "Id": "GUID FROM APP MANIFEST",
        "Role": "ManageDevices",
        "AllowedActions": [
          "CreateDevices",
          "UpdateDevices",
          "DeleteDevices"
        ]
      }
    ]
    }
    ```

1. Po dokončení úprav **Services/data/policies/roles.json** souboru, sestavili a mikroslužeb ověřování a autorizace pro akcelerátor řešení.

### <a name="how-the-web-ui-enforces-permissions"></a>Jak ve webovém uživatelském rozhraní vynucuje oprávnění

Webové uživatelské rozhraní používá [ověřování a autorizace mikroslužeb](https://github.com/Azure/pcs-auth-dotnet) rozhodnout, jaké akce uživatele může vzít a jaké ovládací prvky jsou zobrazeny v uživatelském rozhraní. Například, pokud vaše řešení se nazývá **contoso-rm4**, webové uživatelské rozhraní načte seznam povolených akcí pro aktuálního uživatele odesláním požadavku následující:

```http
http://contoso-rm4.azurewebsites.net/v1/users/current
headers:
X-Source: true
Authorization: Bearer <JWT Token from ADAL>
```

Volá se, pro uživatele **Správce zařízení** v **ManageDevices** role, odpovědi obsahuje následující kód JSON v textu:

```json
{
  "Id": "userIdExample",
  "Email": "devicemanager@contoso.com",
  "Name": "Device Manager",
  "AllowedActions": [
    "CreateDevices",
    "UpdateDevices",
    "DeleteDevices"
  ]
}
```

Následující fragment kódu ze [deviceDelete.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/pages/devices/flyouts/deviceDelete/deviceDelete.js) v [webové uživatelské rozhraní](https://github.com/Azure/pcs-remote-monitoring-webui/) ukazuje, jak oprávnění jsou vynucena deklarativně:

```json
<FlyoutContent>
  <Protected permission={permissions.deleteDevices}>
    <form className="device-delete-container" onSubmit={this.deleteDevices}>
      ...
    </form>
  </Protected>
</FlyoutContent>
```

Další informace najdete v tématu [součástí chráněných](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/shared/protected/README.md). Můžete definovat další oprávnění v [authModel.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/services/models/authModels.js) souboru.

### <a name="how-the-microservices-enforce-permissions"></a>Jak mikroslužby vynutit oprávnění

Mikroslužby se taky podívat oprávnění k ochraně proti neoprávněné požadavky rozhraní API. Když mikroslužbě obdrží požadavek na rozhraní API, dekóduje a ověří token JWT k získání ID uživatele a oprávnění přidružené k roli uživatele.

Následující fragment kódu ze [DevicesController.cs](https://github.com/Azure/iothub-manager-dotnet/blob/master/WebService/v1/Controllers/DevicesController.cs) soubor [IoTHub správce mikroslužeb](https://github.com/Azure/iothub-manager-dotnet), ukazuje, jak funguje dodržování oprávnění:

```csharp
[HttpDelete("{id}")]
[Authorize("DeleteDevices")]
public async Task DeleteAsync(string id)
{
    await this.devices.DeleteAsync(id);
}
```

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak na základě rolí přístup ovládací prvky jsou implementovány v akcelerátoru řešení vzdáleného monitorování.

Další koncepční informace o akcelerátoru řešení vzdáleného monitorování najdete v tématu [architektura vzdáleného monitorování](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Další informace o přizpůsobení řešení vzdáleného monitorování najdete v tématu [přizpůsobení a opětovné nasazení mikroslužby](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->