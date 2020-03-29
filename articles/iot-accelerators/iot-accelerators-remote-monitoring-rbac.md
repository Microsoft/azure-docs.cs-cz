---
title: Řízení přístupu vzdáleného monitorování – Azure | Dokumenty společnosti Microsoft
description: Tento článek obsahuje informace o konfiguraci ovládacích prvků přístupu založených na rolích (RBAC) v akcelerátoru řešení vzdáleného monitorování
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: b0c9699bccbb539c9617fac2f3296483139e7188
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67203170"
---
# <a name="configure-role-based-access-controls-in-the-remote-monitoring-solution-accelerator"></a>Konfigurace ovládacích prvků přístupu založených na rolích v akcelerátoru řešení vzdáleného monitorování

Tento článek obsahuje informace o konfiguraci ovládacích prvků přístupu založených na rolích v akcelerátoru řešení vzdáleného monitorování. Ovládací prvky přístupu založené na rolích umožňují omezit přístup jednotlivých uživatelů ke konkrétním funkcím v řešení.

## <a name="default-settings"></a>Výchozí nastavení

Při prvním nasazení řešení vzdáleného monitorování existují dvě role: **Správce** a **Jen pro čtení**.

Každý uživatel v roli **správce** má úplný přístup k řešení, včetně následujících oprávnění níže. Uživatel v roli **Jen pro čtení** bude mít přístup pouze k zobrazení řešení.

| Oprávnění            | Správce | Jen pro čtení |
|----------------       |-------|-----------|
| Zobrazit řešení         | Ano   | Ano       |
| Aktualizace alarmů         | Ano   | Ne        |
| Odstranění alarmů         | Ano   | Ne        |
| Vytváření zařízení        | Ano   | Ne        |
| Aktualizace zařízení        | Ano   | Ne        |
| Odstranění zařízení        | Ano   | Ne        |
| Vytvoření skupin zařízení  | Ano   | Ne        |
| Aktualizace skupin zařízení  | Ano   | Ne        |
| Odstranění skupin zařízení  | Ano   | Ne        |
| Vytvořit pravidla          | Ano   | Ne        |
| Aktualizovat pravidla          | Ano   | Ne        |
| Odstranit pravidla          | Ano   | Ne        |
| Vytváření úloh           | Ano   | Ne        |
| Aktualizovat správu SIM karet | Ano   | Ne        |

Ve výchozím nastavení je uživateli, který nasadil řešení, automaticky přiřazena role **správce** a je vlastníkem aplikace Azure Active Directory. Jako vlastník aplikace můžete přiřadit role ostatním uživatelům prostřednictvím portálu Azure. Pokud chcete, aby jiný uživatel přiřadil role v řešení, musí být také nastaven jako vlastník aplikace na webu Azure Portal.

> [!NOTE]
> Uživatel, který nasadil řešení, je **jedinou osobou,** která jej může zobrazit ihned po jeho vytvoření. Pokud chcete ostatním udělit přístup k zobrazení aplikace jako role Jen pro čtení, Správce nebo Vlastní, podívejte se na následující pokyny níže v tématu Přidání nebo odebrání uživatelů.

## <a name="add-or-remove-users"></a>Přidání nebo odebrání uživatelů

Jako vlastník aplikace Azure Active Directory můžete pomocí portálu Azure přidat nebo odebrat uživatele do role z řešení vzdáleného monitorování. Následující kroky používají [podnikovou aplikaci Azure Active Directory,](../active-directory/manage-apps/add-application-portal.md#find-your-azure-ad-tenant-application) která byla vytvořena při nasazení řešení vzdáleného monitorování.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. Zkontrolujte, zda [je uživatel v adresáři,](../active-directory/fundamentals/add-users-azure-active-directory.md) který používáte. Adresář jste zvolili při přihlášení k webu [Akcelerátory řešení Microsoft Azure IoT.](https://www.azureiotsolutions.com/Accelerators) Název adresáře je viditelný v pravém horním rohu [stránky](https://www.azureiotsolutions.com/Accelerators).

1. Nawebunajdete **aplikaci Enterprise** pro své řešení na webu Azure Portal. Poté, co tam, filtrovat seznam nastavením **typ aplikace** na **všechny aplikace**. Vyhledejte aplikaci podle názvu aplikace. Název aplikace je název řešení vzdáleného monitorování. Na následujícím snímku obrazovky jsou zobrazované názvy řešení a aplikací **contoso-rm4**.

    ![Podniková aplikace](media/iot-accelerators-remote-monitoring-rbac/appregistration.png)

1. Kliknutím na aplikaci a kliknutím na **Owners**zkontrolujte, zda jste vlastníkem aplikace. Na následujícím snímku obrazovky je **správce společnosti Contoso** vlastníkem aplikace **contoso-rm4:**

    ![Vlastníci](media/iot-accelerators-remote-monitoring-rbac/owners.png)

    Pokud nejste vlastníkem, musíte požádat stávajícího vlastníka, aby vás přidal do seznamu. Pouze vlastníci mohou přiřadit role aplikace, jako je **správce** nebo **jen pro čtení,** ostatním uživatelům.

1. Chcete-li zobrazit seznam uživatelů přiřazených k rolím v aplikaci, klepněte na tlačítko **Uživatelé a skupiny**.

1. Chcete-li přidat uživatele, klepněte na tlačítko **+ Přidat uživatele**a potom klepněte na **položku Uživatelé a skupiny, Možnost Výběr a výběr** uživatele z adresáře.

1. Chcete-li přiřadit uživatele k roli, klepněte na **tlačítko Vybrat roli, Možnost Žádná vybraná** a zvolte roli **Správce** nebo Jen **pro čtení** pro uživatele. Klepněte na tlačítko **Vybrat**a potom klepněte na tlačítko **Přiřadit**.

    ![Výběr role](media/iot-accelerators-remote-monitoring-rbac/selectrole.png)

1. Uživatel má nyní přístup k řešení vzdáleného monitorování s oprávněními definovanými rolí.

1. Uživatele můžete odstranit z aplikace na stránce **Uživatelé a skupiny** na portálu.

## <a name="create-a-custom-role"></a>Vytvoření vlastní role

Řešení vzdáleného monitorování zahrnuje role **správce** a **jen pro čtení** při prvním nasazení. Můžete přidat vlastní role s různými sadami oprávnění. Chcete-li definovat vlastní roli, je třeba:

- Přidejte novou roli do aplikace na webu Azure Portal.
- Definujte zásadu pro novou roli v mikroslužbě ověřování a autorizace.
- Aktualizujte webové uživatelské uživatelské informace řešení.

### <a name="define-a-custom-role-in-the-azure-portal"></a>Definování vlastní role na webu Azure Portal

Následující kroky popisují, jak přidat roli do aplikace ve službě Azure Active Directory. V tomto příkladu vytvoříte novou roli, která umožňuje členům vytvářet, aktualizovat a odstraňovat zařízení v řešení vzdáleného monitorování.

1. Našel **registraci aplikace** pro vaše řešení na webu Azure Portal. Název aplikace je název řešení vzdáleného monitorování. Na následujícím snímku obrazovky jsou zobrazované názvy řešení a aplikací **contoso-rm4**.

    ![Registrace aplikací](media/iot-accelerators-remote-monitoring-rbac/app-registration-2.png)

1. Vyberte aplikaci a klepněte na **položku Manifest**. Můžete vidět dvě existující [role aplikace](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles) definované pro aplikaci:

    ![Zobrazit manifest](media/iot-accelerators-remote-monitoring-rbac/view-manifest.png)

1. Upravte manifest a přidejte roli s názvem **ManageDevices,** jak je znázorněno v následujícím fragmentu. Potřebujete jedinečný řetězec, například identifikátor GUID pro nové ID role. Pomocí služby, jako je například [online generátor identifikátorů GUID,](https://www.guidgenerator.com/)můžete vygenerovat nový identifikátor GUID :

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

### <a name="define-a-policy-for-the-new-role"></a>Definování zásad pro novou roli

Po přidání role do aplikace na webu Azure Portal, musíte definovat zásadu v [roles.json](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/auth/Services/data/policies/roles.json) pro roli, která přiřazuje oprávnění potřebná ke správě zařízení.

1. Klonujte úložiště [mikroslužeb vzdáleného monitorování](https://github.com/Azure/remote-monitoring-services-dotnet) z GitHubu do místního počítače.

1. Upravte soubor **auth/Services/data/policies/roles.json** a přidejte zásady pro roli **ManageDevices,** jak je znázorněno v následujícím fragmentu. Hodnoty **ID** a **Role** musí odpovídat definici role v manifestu aplikace z předchozí části. Seznam povolených akcí umožňuje někomu v roli **ManageDevices** vytvářet, aktualizovat a odstraňovat zařízení připojená k řešení:

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

1. Po dokončení úprav souboru **Services/data/policies/roles.json** znovu a znovu nasaďte mikroslužbu ověřování a autorizace do akcelerátoru řešení.

### <a name="how-the-web-ui-enforces-permissions"></a>Jak webové uživatelské uživatelské nastavení vynucuje oprávnění

Webové uživatelské rozhraní používá [ověřování a autorizace mikroslužby](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) k určení, jaké akce uživatel může provést a jaké ovládací prvky jsou viditelné v uživatelském rozhraní. Pokud se například vaše řešení nazývá **contoso-rm4**, webové uživatelské rozhraní načte seznam povolených akcí pro aktuálního uživatele odesláním následujícího požadavku:

```http
http://contoso-rm4.azurewebsites.net/v1/users/current
headers:
X-Source: true
Authorization: Bearer <JWT Token from ADAL>
```

Pro uživatele s názvem **Správce zařízení** v roli **ManageDevices** odpověď zahrnuje následující JSON v těle:

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

Následující úryvek z [deviceDelete.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/pages/devices/flyouts/deviceDelete/deviceDelete.js) ve [webovém uživatelském rozhraní](https://github.com/Azure/pcs-remote-monitoring-webui/) ukazuje, jak jsou oprávnění deklarativně vynucována:

```json
<FlyoutContent>
  <Protected permission={permissions.deleteDevices}>
    <form className="device-delete-container" onSubmit={this.deleteDevices}>
      ...
    </form>
  </Protected>
</FlyoutContent>
```

Další informace naleznete [v tématu Chráněné součásti](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/shared/protected/README.md). V [souboru authModel.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/services/models/authModels.js) můžete definovat další oprávnění.

### <a name="how-the-microservices-enforce-permissions"></a>Jak mikroslužby vynucují oprávnění

Mikroslužby také zkontrolovat oprávnění k ochraně proti neoprávněným požadavkům rozhraní API. Když mikroslužba obdrží požadavek rozhraní API, dekóduje a ověřuje token JWT získat ID uživatele a oprávnění spojená s rolí uživatele.

Následující úryvek ze souboru [DevicesController.cs](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/iothub-manager/WebService/v1/Controllers/DevicesController.cs) v [mikroslužbě Správce IoTHub](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager)udává, jak jsou vynucena oprávnění:

```csharp
[HttpDelete("{id}")]
[Authorize("DeleteDevices")]
public async Task DeleteAsync(string id)
{
    await this.devices.DeleteAsync(id);
}
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak jsou implementovány ovládací prvky přístupu založené na rolích v akcelerátoru řešení vzdáleného monitorování.

Informace o správě přístupu k průzkumníku Time Series Insights v akcelerátoru řešení vzdáleného monitorování naleznete v [tématu Konfigurace ovládacích prvků přístupu pro průzkumníka Time Series Insights.](iot-accelerators-remote-monitoring-rbac-tsi.md)

Další koncepční informace o akcelerátoru řešení vzdáleného monitorování naleznete v [tématu Architektura vzdáleného monitorování](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Další informace o přizpůsobení řešení vzdáleného monitorování naleznete v [tématu Přizpůsobení a opětovné nasazení mikroslužeb.](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->