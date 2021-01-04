---
title: Připojení ServiceNow ke konektoru pro správu služeb IT
description: Tento článek poskytuje informace o tom, jak ServiceNow pomocí konektoru IT Service Management Connector (ITSMC) v Azure Monitor centrálně monitorovat a spravovat pracovní položky ITSM.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: 662c36e4f0082c376a6e250e9a0885f0cd225964
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2020
ms.locfileid: "97729625"
---
# <a name="connect-servicenow-with-it-service-management-connector"></a>Připojení ServiceNow ke konektoru pro správu služeb IT

Tento článek poskytuje informace o tom, jak nakonfigurovat připojení mezi instancí ServiceNow a konektorem Service Management Connector (ITSMC) v Log Analytics pro centrální správu vašich pracovních položek.

Následující části obsahují podrobné informace o tom, jak připojit produkt ServiceNow k ITSMC v Azure.

## <a name="prerequisites"></a>Požadavky
Ujistěte se, že jsou splněné následující předpoklady:
- ITSMC je nainstalovaný. Další informace: [Přidání řešení IT Service Management Connector](./itsmc-definition.md#add-it-service-management-connector).
- Podporované verze ServiceNow: Orlandu, New York, Madrid, Londýn, Kingston, Jakarta, Istanbul, Helsinky, Ženeva.
- V současné době se výstrahy odesílané z Azure Monitor můžou vytvořit v ServiceNow jednom z následujících prvků: události, incidenty nebo výstrahy.
> [!NOTE]
> ITSMC podporuje nyní jenom oficiální nabídku SaaS ze služby. Soukromá nasazení služby teď nejsou podporovaná. 

**Správci ServiceNow musí v rámci své instance ServiceNow provádět následující**:
- Vygenerujte ID klienta a tajný klíč klienta pro produkt ServiceNow. Informace o tom, jak vygenerovat ID a tajný kód klienta, najdete v následujících informacích:

    - [Nastavení OAuth pro Orlandu](https://docs.servicenow.com/bundle/orlando-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Nastavit OAuth pro New York](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Nastavte OAuth pro Madrid.](https://docs.servicenow.com/bundle/madrid-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Nastavení OAuth pro Londýn](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Nastavení OAuth pro Kingston](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Nastavení OAuth pro Jakarta](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Nastavte OAuth pro Istanbul.](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Nastavte OAuth pro Helsinky.](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Nastavte OAuth pro Ženeva.](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)
> [!NOTE]
> Jako součást definice "nastavení OAuth" doporučujeme:
>
> 1) **Aktualizujte obnovovací token životnosti na 90 dní (7 776 000 sekund):** Jako součást [Nastavení OAuth](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.servicenow.com%2Fbundle%2Fnewyork-platform-administration%2Fpage%2Fadminister%2Fsecurity%2Ftask%2Ft_SettingUpOAuth.html&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C2c6812e429a549e71cdd08d7d1b148d8%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637208431696739125&sdata=Q7mF6Ej8MCupKaEJpabTM56EDZ1T8vFVyihhoM594aA%3D&reserved=0) ve fázi 2: [vytvořte koncový bod pro klienty, kteří budou mít přístup k instanci](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.servicenow.com%2Fbundle%2Fnewyork-platform-administration%2Fpage%2Fadminister%2Fsecurity%2Ftask%2Ft_CreateEndpointforExternalClients.html&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C2c6812e429a549e71cdd08d7d1b148d8%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637208431696749123&sdata=hoAJHJAFgUeszYCX1Q%2FXr4N%2FAKiFcm5WV7mwR2UqeWA%3D&reserved=0) po definici koncového bodu, v okně ServiceNow v okně vyhledejte systém OAuth než vyberte registr aplikace. Vyberte název nadefinovaného protokolu OAuth a aktualizujte pole obnovovacího tokenu na 7 776 000 (90 dní v sekundách).
> Na konci klikněte na aktualizovat.
> 2) Doporučujeme, abyste **navázali interní postup, který zajistí, že připojení zůstane aktivní:** Podle životnosti obnovovacího tokenu aktualizujte token. Ujistěte se prosím, že jste provedli následující operace: předchozí doba platnosti tokenu obnovení (několik dní, než vyprší platnost životnosti obnovovacího tokenu, doporučujeme):
>
>     1. [Dokončení procesu ruční synchronizace pro konfiguraci konektoru ITSM](./itsmc-resync-servicenow.md)
>     2. Odvolat starý obnovovací token, protože nedoporučujeme z z bezpečnostních důvodů zachovat staré klíče. V okně ServiceNow vyhledejte systém OAuth, než vyberte Spravovat tokeny. Ze seznamu vyberte starý token podle názvu OAuth a data vypršení platnosti.
> ![SNĚH – definice OAuth systému](media/itsmc-connections/snow-system-oauth.png)
>     3. Klikněte na odvolat přístup a než na odvolat.

- Nainstalujte uživatelskou aplikaci pro Microsoft Log Analytics Integration (aplikace ServiceNow). [Přečtěte si další informace](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 ).
> [!NOTE]
> ITSMC podporuje pouze oficiální uživatelskou aplikaci pro integraci se službou Microsoft Log Analytics, která je stažena z úložiště ServiceNow. ITSMC nepodporují ingestování kódu na ServiceNow straně nebo v aplikaci, která není součástí oficiálního řešení ServiceNow. 
- Vytvoření role uživatele integrace pro uživatelskou aplikaci nainstalovanou. [Tady najdete](#create-integration-user-role-in-servicenow-app)informace o tom, jak vytvořit roli uživatele Integration.

## <a name="connection-procedure"></a>**Postup připojení**
K vytvoření připojení ServiceNow použijte následující postup:


1. V Azure Portal přejít na **všechny prostředky** a vyhledejte **partnera (YourWorkspaceName)** .

2.  V části **zdroje dat pracovního prostoru** klikněte na **připojení ITSM**.
    ![Nové připojení](media/itsmc-connections/add-new-itsm-connection.png)

3. V horní části pravého podokna klikněte na **Přidat**.

4. Zadejte informace, jak je popsáno v následující tabulce, a kliknutím na tlačítko **OK** vytvořte připojení.


> [!NOTE]
> Všechny tyto parametry jsou povinné.

| **Pole** | **Popis** |
| --- | --- |
| **Název připojení**   | Zadejte název instance ServiceNow, ke které se chcete připojit pomocí ITSMC.  Tento název použijete později v Log Analytics, když konfigurujete pracovní položky v tomto ITSM/zobrazení podrobné Log Analytics. |
| **Typ partnera**   | Vyberte **ServiceNow**. |
| **Uživatelské jméno**   | Zadejte uživatelské jméno pro integraci, které jste vytvořili v aplikaci ServiceNow, aby se podporovalo připojení k ITSMC. Další informace: [vytvoření role uživatele aplikace ServiceNow](#create-integration-user-role-in-servicenow-app)|
| **Heslo**   | Zadejte heslo přidružené k tomuto uživatelskému jménu. **Poznámka**: uživatelské jméno a heslo se používají jenom k vytváření tokenů ověřování a nejsou uložené kdekoli v rámci služby ITSMC.  |
| **Adresa URL serveru**   | Zadejte adresu URL instance ServiceNow, ke které se chcete připojit ITSMC. Adresa URL by měla ukazovat na podporovanou verzi SaaS s příponou. servicenow.com.|
| **ID klienta**   | Zadejte ID klienta, které chcete použít pro OAuth2 ověřování, které jste vygenerovali dříve.  Další informace o generování ID klienta a tajného klíče:   [instalace OAuth](https://wiki.servicenow.com/index.php?title=OAuth_Setup). |
| **Tajný kód klienta**   | Zadejte tajný klíč klienta generovaný pro toto ID.   |
| **Rozsah synchronizace dat**   | Vyberte ServiceNow pracovní položky, které chcete synchronizovat s Azure Log Analytics prostřednictvím ITSMC.  Vybrané hodnoty se importují do Log Analytics.   **Možnosti:**  Incidenty a žádosti o změnu.|
| **Synchronizovat data** | Zadejte počet uplynulých dní, z nichž mají být data. **Maximální limit**: 120 dní. |
| **Vytvořit novou položku konfigurace v řešení ITSM** | Tuto možnost vyberte, pokud chcete vytvořit položky konfigurace v produktu ITSM. Pokud je tato možnost vybrána, ITSMC vytvoří v podporovaném systému ITSM v případě neexistujícího systému služby CI ovlivněné položky konfigurace. **Výchozí**: zakázáno. |

![Připojení ServiceNow](media/itsmc-connections/itsm-connection-servicenow-connection-latest.png)

**Po úspěšném připojení a synchronizaci**:

- Vybrané pracovní položky z instance ServiceNow se importují do Azure **Log Analytics.** Souhrn těchto pracovních položek můžete zobrazit na dlaždici konektoru pro IT služby IT.

- Můžete vytvářet incidenty z výstrah Log Analytics nebo ze záznamů protokolů nebo z výstrah Azure v této instanci ServiceNow.

> [!NOTE]
> V ServiceNow platí omezení přenosové rychlosti pro žádosti za hodinu. Pokud chcete omezení nakonfigurovat, použijte k tomu definování "omezení rychlosti příchozího REST API" v instanci ServiceNow.

## <a name="create-integration-user-role-in-servicenow-app"></a>Vytvoření role uživatele integrace v aplikaci ServiceNow

Uživatel následující postup:

1. Navštivte web [ServiceNow Store](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) a nainstalujte si **uživatelskou aplikaci pro ServiceNow a integraci Microsoft OMS** do instance ServiceNow.
   
   >[!NOTE]
   >V rámci průběžného přechodu z Microsoft Operations Management Suite (OMS) na Azure Monitor se v OMS teď označuje jako Log Analytics.     
2. Po instalaci navštivte levý navigační panel instance ServiceNow, vyhledejte a vyberte Microsoft OMS integrátor.  
3. Klikněte na **Kontrolní seznam instalace**.

   Stav se zobrazí jako  **nedokončený** , pokud je role uživatele stále vytvořena.

4. Do textových polí vedle pole **vytvořit uživatele integrace** zadejte uživatelské jméno pro uživatele, který se může připojit k ITSMC v Azure.
5. Zadejte heslo pro tohoto uživatele a klikněte na tlačítko **OK**.  

> [!NOTE]
> Tyto přihlašovací údaje použijete k vytvoření připojení ServiceNow v Azure.

Nově vytvořený uživatel se zobrazí s přiřazenými výchozími rolemi.

**Výchozí role**:
- personalize_choices
- import_transformer
-   x_mioms_microsoft. User
-   Standard
-   template_editor
-   view_changer

Po úspěšném vytvoření uživatele se stav **kontrolního seznamu kontroly instalace** přesune na dokončeno a zobrazí se seznam podrobností role uživatele vytvořené pro danou aplikaci.

> [!NOTE]
> Konektor ITSM může odesílat incidenty do ServiceNow bez dalších modulů nainstalovaných v instanci ServiceNow. Pokud v instanci ServiceNow používáte modul EventManagement a chcete v ServiceNow vytvářet události nebo výstrahy pomocí konektoru, přidejte do uživatele integrace následující role:
> 
>    - evt_mgmt_integration
>    - evt_mgmt_operator  


## <a name="next-steps"></a>Další kroky

* [Přehled konektoru ITSM](itsmc-overview.md)
* [Vytváření pracovních položek ITSM z výstrah Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Řešení potíží v ITSM konektoru](./itsmc-resync-servicenow.md)