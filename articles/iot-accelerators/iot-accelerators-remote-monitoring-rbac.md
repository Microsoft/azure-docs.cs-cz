---
title: Řízení přístupu vzdáleného monitorování – Azure | Microsoft Docs
description: Tento článek poskytuje informace o tom, jak můžete v akcelerátoru řešení vzdáleného monitorování nakonfigurovat řízení přístupu na základě role (RBAC).
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.custom: has-adal-ref, devx-track-csharp
ms.openlocfilehash: 817bc7624bb3a6b69d56265e40681287b7fc09fa
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90969591"
---
# <a name="configure-role-based-access-control-in-the-remote-monitoring-solution-accelerator"></a>Konfigurace řízení přístupu na základě role v akcelerátoru řešení vzdáleného monitorování

Tento článek poskytuje informace o tom, jak nakonfigurovat řízení přístupu na základě role v akcelerátoru řešení vzdáleného monitorování. Řízení přístupu na základě rolí umožňuje omezit přístup pro jednotlivé uživatele na konkrétní funkce v řešení.

## <a name="default-settings"></a>Výchozí nastavení

Při prvním nasazení řešení vzdáleného monitorování jsou k dispozici dvě role: **správce** a **jen pro čtení**.

Každý uživatel v roli **správce** má úplný přístup k řešení, včetně níže uvedených oprávnění. Uživatel v roli jen **pro čtení** bude mít přístup jenom k zobrazení řešení.

| Oprávnění            | Správce | Jen pro čtení |
|----------------       |-------|-----------|
| Zobrazit řešení         | Yes   | Yes       |
| Aktualizovat alarmy         | Yes   | No        |
| Odstranit alarmy         | Yes   | No        |
| Vytváření zařízení        | Yes   | No        |
| Aktualizace zařízení        | Yes   | No        |
| Odstranit zařízení        | Yes   | No        |
| Vytvoření skupin zařízení  | Yes   | No        |
| Aktualizace skupin zařízení  | Yes   | No        |
| Odstranění skupin zařízení  | Yes   | No        |
| Vytvořit pravidla          | Yes   | No        |
| Aktualizovat pravidla          | Yes   | No        |
| Odstranit pravidla          | Yes   | No        |
| Vytváření úloh           | Yes   | No        |
| Aktualizace správy SIM | Yes   | No        |

Ve výchozím nastavení se uživateli, který toto řešení nasadí, automaticky přiřadí role **správce** a je vlastníkem aplikace Azure Active Directory. Jako vlastník aplikace můžete přiřadit role jiným uživatelům prostřednictvím Azure Portal. Pokud chcete, aby jiný uživatel přidělil role v řešení, musí být také nastaven jako vlastník aplikace v Azure Portal.

> [!NOTE]
> Uživatel, který řešení nasadil, je **jediná osoba** , která ji může zobrazit ihned po jejím vytvoření. Pokud chcete ostatním uživatelům udělit přístup k zobrazení aplikace buď jako jen pro čtení, správce nebo vlastní roli, přečtěte si následující pokyny v tématu Přidání nebo odebrání uživatelů.

## <a name="add-or-remove-users"></a>Přidat nebo odebrat uživatele

Jako vlastník aplikace Azure Active Directory můžete pomocí Azure Portal přidat nebo odebrat uživatele k roli z řešení vzdáleného monitorování. Následující kroky používají [Azure Active Directory podnikovou aplikaci](../active-directory/manage-apps/view-applications-portal.md) , která byla vytvořena při nasazení řešení vzdáleného monitorování.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Ověřte, že se [uživatel nachází v adresáři](../active-directory/fundamentals/add-users-azure-active-directory.md) , který používáte. Zvolili jste adresář, který se má použít, když jste přihlášeni k webu [akcelerátory řešení Microsoft Azure IoT](https://www.azureiotsolutions.com/Accelerators) . Název adresáře je viditelný v pravém horním rohu [stránky](https://www.azureiotsolutions.com/Accelerators).

1. Najděte **podnikovou aplikaci** pro vaše řešení v Azure Portal. Až tam budete, vyfiltrujte seznam nastavením **Typ aplikace** na **všechny aplikace**. Vyhledejte aplikaci podle názvu aplikace. Název aplikace je název vašeho řešení vzdáleného monitorování. Na následujícím snímku obrazovky jsou zobrazované názvy řešení a aplikace **Contoso-RM4**.

    ![Podniková aplikace](media/iot-accelerators-remote-monitoring-rbac/appregistration.png)

1. Ověřte, že jste vlastníkem aplikace kliknutím na aplikaci a potom kliknutím na **vlastníci**. Na následujícím snímku obrazovky je **správce Contoso** vlastníkem aplikace **Contoso-RM4** :

    ![Snímek obrazovky zobrazuje vybranou možnost spravovat vlastníky, která zobrazuje vlastníky aplikace Contoso r m 4.](media/iot-accelerators-remote-monitoring-rbac/owners.png)

    Pokud nejste vlastníkem, musíte požádat stávajícího vlastníka, aby vás přidal do seznamu. Pouze vlastníci mohou přiřadit aplikační role, jako je například **správce** nebo **jen pro čtení** , jiným uživatelům.

1. Chcete-li zobrazit seznam uživatelů přiřazených k rolím v aplikaci, klikněte na možnost **Uživatelé a skupiny**.

1. Chcete-li přidat uživatele, klikněte na tlačítko **+ Přidat uživatele**a pak klikněte na možnost **Uživatelé a skupiny. možnost není vybrána,** Chcete-li vybrat uživatele z adresáře.

1. Pokud chcete uživatele přiřadit k roli, klikněte na **Vybrat roli, žádná vybraná** a pro uživatele zvolte roli **správce** nebo jen **pro čtení** . Klikněte na **Vybrat**a pak na **přiřadit**.

    ![Vybrat roli](media/iot-accelerators-remote-monitoring-rbac/selectrole.png)

1. Uživatel teď může získat přístup k řešení vzdáleného monitorování s oprávněními definovanými rolí.

1. Uživatele můžete odstranit z aplikace na stránce **Uživatelé a skupiny** na portálu.

## <a name="create-a-custom-role"></a>Vytvoření vlastní role

Řešení vzdáleného monitorování zahrnuje role **správce** a jen **pro čtení** při prvním nasazení. Můžete přidat vlastní role s různými sadami oprávnění. K definování vlastní role potřebujete:

- Přidejte do aplikace novou roli v Azure Portal.
- Definujte zásadu nové role v mikroslužbě ověřování a autorizace.
- Aktualizujte webové uživatelské rozhraní řešení.

### <a name="define-a-custom-role-in-the-azure-portal"></a>Definování vlastní role v Azure Portal

Následující postup popisuje, jak přidat roli do aplikace v Azure Active Directory. V tomto příkladu vytvoříte novou roli, která umožňuje členům vytvářet, aktualizovat a odstraňovat zařízení v řešení vzdáleného monitorování.

1. Vyhledejte v Azure Portal **registraci aplikace** pro vaše řešení. Název aplikace je název vašeho řešení vzdáleného monitorování. Na následujícím snímku obrazovky jsou zobrazované názvy řešení a aplikace **Contoso-RM4**.

    ![Registrace aplikace](media/iot-accelerators-remote-monitoring-rbac/app-registration-2.png)

1. Vyberte svou aplikaci a pak klikněte na **manifest**. Můžete si zobrazit dvě existující [role aplikace](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles) definované pro aplikaci:

    ![Zobrazit manifest](media/iot-accelerators-remote-monitoring-rbac/view-manifest.png)

1. Upravte manifest pro přidání role s názvem **ManageDevices** , jak je znázorněno v následujícím fragmentu kódu. Pro nové ID role potřebujete jedinečný řetězec, jako je například identifikátor GUID. Můžete vygenerovat nový identifikátor GUID pomocí služby, jako je například [online generátor GUID](https://www.guidgenerator.com/):

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

### <a name="define-a-policy-for-the-new-role"></a>Definujte zásadu pro novou roli.

Po přidání role do aplikace v Azure Portal musíte definovat zásadu v [roles.js](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/auth/Services/data/policies/roles.json) pro roli, která přiřazuje oprávnění potřebná ke správě zařízení.

1. Naklonujte úložiště [mikroslužeb vzdáleného monitorování](https://github.com/Azure/remote-monitoring-services-dotnet) z GitHubu do místního počítače.

1. Chcete-li přidat zásadu pro roli **ManageDevices** , jak je znázorněno v následujícím fragmentu kódu, upravte pole **ověřování/služby/data/zásady/roles.jsna** soubor. Hodnoty **ID** a **role** se musí shodovat s definicí role v manifestu aplikace z předchozí části. Seznam povolených akcí umožňuje někomu v roli **ManageDevices** vytvářet, aktualizovat a odstraňovat zařízení připojená k řešení:

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

1. Po dokončení úprav **služeb/dat/zásad/roles.jsv** souboru znovu sestavte a znovu nasaďte mikroslužbu pro ověřování a autorizaci do svého akcelerátoru řešení.

### <a name="how-the-web-ui-enforces-permissions"></a>Způsob, jakým webové uživatelské rozhraní vynutilo oprávnění

Webové uživatelské rozhraní používá [mikroslužbu ověřování a autorizaci](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) k určení akcí, které může uživatel provést a které ovládací prvky jsou viditelné v uživatelském rozhraní. Pokud se například vaše řešení nazývá **Contoso-RM4**, uživatelské rozhraní Web načte seznam povolených akcí pro aktuálního uživatele odesláním následující žádosti:

```http
http://contoso-rm4.azurewebsites.net/v1/users/current
headers:
X-Source: true
Authorization: Bearer <JWT Token from ADAL>
```

Pro uživatele s názvem **Device Manager** v roli **ManageDevices** odpověď obsahuje následující JSON v těle:

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

Následující fragment kódu z [deviceDelete.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/pages/devices/flyouts/deviceDelete/deviceDelete.js) ve [webovém uživatelském rozhraní](https://github.com/Azure/pcs-remote-monitoring-webui/) ukazuje, jak jsou oprávnění nastavena deklarativně:

```json
<FlyoutContent>
  <Protected permission={permissions.deleteDevices}>
    <form className="device-delete-container" onSubmit={this.deleteDevices}>
      ...
    </form>
  </Protected>
</FlyoutContent>
```

Další informace najdete v tématu [chráněné součásti](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/shared/protected/README.md). V souboru [authModel.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/services/models/authModels.js) můžete definovat další oprávnění.

### <a name="how-the-microservices-enforce-permissions"></a>Jak mikroslužby vynutily oprávnění

Mikroslužby také kontrolují oprávnění k ochraně před neoprávněnými požadavky rozhraní API. Když mikroslužba obdrží požadavek rozhraní API, dekóduje a ověří token JWT, aby získala ID uživatele a oprávnění související s rolí uživatele.

Následující fragment kódu ze souboru [DevicesController.cs](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/iothub-manager/WebService/v1/Controllers/DevicesController.cs) ve [mikroslužbě IoTHub Manager](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager)ukazuje, jak se vynutila oprávnění:

```csharp
[HttpDelete("{id}")]
[Authorize("DeleteDevices")]
public async Task DeleteAsync(string id)
{
    await this.devices.DeleteAsync(id);
}
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak se v akcelerátoru řešení vzdáleného monitorování implementuje řízení přístupu na základě rolí.

Informace o správě přístupu k Průzkumníkovi Time Series Insights v akcelerátoru řešení vzdáleného monitorování najdete v tématu věnovaném [konfiguraci ovládacích prvků přístupu pro průzkumníka Time Series Insights](iot-accelerators-remote-monitoring-rbac-tsi.md) .

Další koncepční informace o akcelerátoru řešení vzdáleného monitorování najdete v tématu [architektura vzdáleného monitorování](iot-accelerators-remote-monitoring-sample-walkthrough.md) .

Další informace o přizpůsobení řešení vzdáleného monitorování najdete v tématu [přizpůsobení a opětovné nasazení mikroslužeb](iot-accelerators-microservices-example.md) .
<!-- Next tutorials in the sequence -->