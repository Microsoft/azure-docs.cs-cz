---
title: IT Service Management Connector v Azure Monitor
description: Tento článek poskytuje informace o tom, jak připojit ITSM produkty/služby ke konektoru IT Service Management Connector (ITSMC) v Azure Monitor centrálně monitorovat a spravovat pracovní položky ITSM.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/12/2020
ms.openlocfilehash: 86fd136cdf03c9bdd6bd1f610cccc339512f8fbc
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657103"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>Propojení produktů nebo služeb ITSM s využitím ITSM konektoru
Tento článek poskytuje informace o tom, jak nakonfigurovat připojení mezi ITSM produktem/službou a konektorem Service Management Connector (ITSMC) v Log Analytics pro centrální správu vašich pracovních položek. Další informace o ITSMC najdete v tématu [Přehled](./itsmc-overview.md).

Podporují se tyto ITSM produkty nebo služby. Výběrem produktu zobrazíte podrobné informace o tom, jak tento produkt připojit k ITSMC.

- [System Center Service Manager](#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](#connect-servicenow-to-it-service-management-connector-in-azure)
- [Prov](#connect-provance-to-it-service-management-connector-in-azure)
- [Cherwell](#connect-cherwell-to-it-service-management-connector-in-azure)

> [!NOTE]
> 
> Naši zákazníci Cherwell a prov navrhují, aby používali [akci Webhooku](./action-groups.md#webhook) pro koncový bod Cherwell a prov jako jiné řešení pro integraci.

## <a name="connect-system-center-service-manager-to-it-service-management-connector-in-azure"></a>Připojení System Center Service Manager ke konektoru pro správu služeb IT v Azure

Následující části obsahují podrobné informace o tom, jak připojit System Center Service Manager produkt k ITSMC v Azure.

### <a name="prerequisites"></a>Požadavky

Ujistěte se, že jsou splněné následující předpoklady:

- ITSMC je nainstalovaný. Další informace: [Přidání řešení IT Service Management Connector](./itsmc-definition.md).
- Webová aplikace Service Manager (webová aplikace) je nasazená a nakonfigurovaná. [Tady](#create-and-deploy-service-manager-web-app-service)jsou informace o webové aplikaci.
- Hybridní připojení se vytvořilo a nakonfigurovalo. Další informace: [Konfigurace hybridního připojení](#configure-the-hybrid-connection).
- Podporované verze Service Manager: 2012 R2 nebo 2016.
- Role uživatele: [operátor pokročilý](/previous-versions/system-center/service-manager-2010-sp1/ff461054(v=technet.10))
- V současné době se můžou výstrahy odeslané z Azure Monitor vytvářet v System Center Service Managerch incidentů.

> [!NOTE]
> 
> - Konektor ITSM se může připojit pouze ke cloudovým instancím ServiceNow. Místní instance ServiceNow se v tuto chvíli nepodporují.
> - Aby bylo možné používat vlastní [šablony](./itsmc-definition.md#template-definitions) jako součást akcí, parametr "ProjectionType" v šabloně SCSM by měl být namapován na "IncidentManagement! System. pracovní položka. incident. ProjectionType "

### <a name="connection-procedure"></a>Postup připojení

K připojení instance System Center Service Manager k ITSMC použijte následující postup:

1. V Azure Portal přejít na **všechny prostředky** a vyhledejte **partnera (YourWorkspaceName)** .

2.  V části **zdroje dat pracovního prostoru** klikněte na **připojení ITSM**.

    ![Nové připojení](media/itsmc-connections/add-new-itsm-connection.png)

3. V horní části pravého podokna klikněte na **Přidat**.

4. Zadejte informace, jak je popsáno v následující tabulce, a kliknutím na tlačítko **OK** vytvořte připojení.

> [!NOTE]
> 
> Všechny tyto parametry jsou povinné.

| **Pole** | **Popis** |
| --- | --- |
| **Název připojení**   | Zadejte název instance System Center Service Manager, ke které se chcete připojit pomocí ITSMC.  Tento název použijete později při konfiguraci pracovních položek v této instanci nebo zobrazení podrobné Log Analytics. |
| **Typ partnera**   | Vyberte **System Center Service Manager**. |
| **Adresa URL serveru**   | Zadejte adresu URL Service Manager webové aplikace. Další informace o Service Manager webové aplikace [najdete tady](#create-and-deploy-service-manager-web-app-service).
| **ID klienta**   | Zadejte ID klienta, které jste vygenerovali (pomocí automatického skriptu) pro ověření webové aplikace. Další informace o automatizovaném skriptu [najdete tady.](./itsmc-service-manager-script.md)|
| **Tajný kód klienta**   | Zadejte tajný klíč klienta generovaný pro toto ID.   |
| **Synchronizovat data**   | Vyberte Service Manager pracovní položky, které chcete synchronizovat pomocí ITSMC.  Tyto pracovní položky jsou importovány do Log Analytics. **Možnosti:**  Incidenty, žádosti o změnu.|
| **Rozsah synchronizace dat** | Zadejte počet uplynulých dní, z nichž mají být data. **Maximální limit**: 120 dní. |
| **Vytvořit novou položku konfigurace v řešení ITSM** | Tuto možnost vyberte, pokud chcete vytvořit položky konfigurace v produktu ITSM. Když se tato možnost vybere, Log Analytics v podporovaném systému ITSM vytvoří ovlivněné položky konfigurace pro SNS (v případě neexistujícího modelu SNS). **Výchozí**: zakázáno. |

![Připojení k Service Manageru](media/itsmc-connections/service-manager-connection.png)

**Po úspěšném připojení a synchronizaci**:

- Vybrané pracovní položky z Service Manager jsou importovány do Azure **Log Analytics.** Souhrn těchto pracovních položek můžete zobrazit na dlaždici konektoru pro IT služby IT.

- Můžete vytvářet incidenty z výstrah Log Analytics nebo ze záznamů protokolů nebo z výstrah Azure v této instanci Service Manager.


Další informace: [vytvoření pracovních položek ITSM z výstrah Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-and-deploy-service-manager-web-app-service"></a>Vytvoření a nasazení služby Service Manager Web App Service

Společnost Microsoft vytvořila Service Manager webovou aplikaci na GitHubu, aby mohla propojit místní Service Manager s ITSMC v Azure.

Pokud chcete pro Service Manager nastavit webovou aplikaci ITSM, postupujte takto:

- **Nasazení webové aplikace** – nasaďte webovou aplikaci, nastavte vlastnosti a proveďte ověření pomocí Azure AD. Webovou aplikaci můžete nasadit pomocí [automatizovaného skriptu](./itsmc-service-manager-script.md) , který vám Microsoft poskytl.
- **Konfigurace hybridního připojení**  -  [Nakonfigurujte toto připojení](#configure-the-hybrid-connection)ručně.

#### <a name="deploy-the-web-app"></a>Nasazení webové aplikace
Pomocí automatizovaného [skriptu](./itsmc-service-manager-script.md) nasaďte webovou aplikaci, nastavte vlastnosti a proveďte ověření pomocí Azure AD.

Spusťte skript zadáním následujících požadovaných podrobností:

- Podrobnosti o předplatném Azure
- Název skupiny prostředků
- Umístění
- Podrobnosti Service Manager serveru (název serveru, doména, uživatelské jméno a heslo)
- Předpona názvu webu pro vaši webovou aplikaci
- Obor názvů ServiceBus

Skript vytvoří webovou aplikaci s názvem, který jste zadali (spolu s několika dalšími řetězci pro její jedinečné nastavení). Vygeneruje **adresu URL webové aplikace**, **ID klienta** a **tajný klíč klienta**.

Uložte hodnoty, které použijete při vytváření připojení pomocí ITSMC.

**Ověření instalace webové aplikace**

1. Přejít na **Azure Portal**  >  **prostředky**.
2. Vyberte webovou aplikaci, klikněte na **Nastavení**  >  **aplikace nastavení**.
3. Potvrďte informace o instanci Service Manager, kterou jste zadali v době nasazení aplikace prostřednictvím skriptu.

### <a name="configure-the-hybrid-connection"></a>Konfigurace hybridního připojení

Následující postup slouží ke konfiguraci hybridního připojení, které spojuje instanci Service Manager s ITSMC v Azure.

1. V části **prostředky Azure** Najděte Service Manager webovou aplikaci.
2. Klikněte na **Nastavení**  >  **sítě**.
3. V části **Hybrid Connections** klikněte na **Konfigurovat koncové body hybridního připojení**.

    ![Sítě hybridního připojení](media/itsmc-connections/itsmc-hybrid-connection-networking-and-end-points.png)
4. V okně **Hybrid Connections** klikněte na **Přidat hybridní připojení**.

    ![Přidat hybridní připojení](media/itsmc-connections/itsmc-new-hybrid-connection-add.png)

5. V okně **přidat Hybrid Connections** klikněte na **vytvořit nové hybridní připojení**.

    ![Nové hybridní připojení](media/itsmc-connections/itsmc-create-new-hybrid-connection.png)

6. Zadejte následující hodnoty:

   - **Název koncového bodu**: zadejte název nového hybridního připojení.
   - **Hostitel koncového bodu**: plně kvalifikovaný název domény Management Server Service Manager.
   - **Port koncového bodu**: typ 5724
   - **ServiceBus obor názvů**: použijte existující obor názvů ServiceBus nebo vytvořte nový.
   - **Umístění**: vyberte umístění.
   - **Název**: zadejte název ServiceBus, pokud ho vytváříte.

     ![Hodnoty hybridního připojení](media/itsmc-connections/itsmc-new-hybrid-connection-values.png)
6. Kliknutím na **OK** zavřete okno **vytvořit hybridní připojení** a začněte vytvářet hybridní připojení.

    Po vytvoření hybridního připojení se zobrazí v okně.

7. Po vytvoření hybridního připojení vyberte připojení a klikněte na **Přidat vybrané hybridní připojení**.

    ![Nové hybridní připojení](media/itsmc-connections/itsmc-new-hybrid-connection-added.png)

#### <a name="configure-the-listener-setup"></a>Konfigurace nastavení naslouchacího procesu

Pomocí následujícího postupu můžete nakonfigurovat nastavení naslouchacího procesu pro hybridní připojení.

1. V okně **Hybrid Connections** klikněte na **Stáhnout Správce připojení** a nainstalujte ho do počítače, na kterém běží System Center Service Manager instance.

    Po dokončení instalace je možnost **správce hybridního připojení uživatelského rozhraní** dostupná v nabídce **Start** .

2. Klikněte na **správce hybridního připojení uživatelské rozhraní** , zobrazí se výzva k zadání přihlašovacích údajů Azure.

3. Přihlaste se pomocí svých přihlašovacích údajů Azure a vyberte předplatné, ve kterém se hybridní připojení vytvořilo.

4. Klikněte na **Uložit**.

Vaše hybridní připojení se úspěšně připojilo.

![hybridní připojení bylo úspěšné.](media/itsmc-connections/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]
> 
> Po vytvoření hybridního připojení ověřte a otestujte připojení návštěvou nasazené webové aplikace Service Manager. Než se pokusíte připojit k ITSMC v Azure, ujistěte se, že je připojení úspěšné.

Následující vzorový obrázek ukazuje podrobné informace o úspěšném připojení:

![Test hybridního připojení](media/itsmc-connections/itsmc-hybrid-connection-test.png)

## <a name="connect-servicenow-to-it-service-management-connector-in-azure"></a>Připojení ServiceNow ke konektoru pro správu služeb IT v Azure

Následující části obsahují podrobné informace o tom, jak připojit produkt ServiceNow k ITSMC v Azure.

### <a name="prerequisites"></a>Požadavky
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
> 1. [Dokončení procesu ruční synchronizace pro konfiguraci konektoru ITSM](./itsmc-resync-servicenow.md)
> 2. Odvolat starý obnovovací token, protože nedoporučujeme z z bezpečnostních důvodů zachovat staré klíče. V okně ServiceNow vyhledejte systém OAuth, než vyberte Spravovat tokeny. Ze seznamu vyberte starý token podle názvu OAuth a data vypršení platnosti.
> ![SNĚH – definice OAuth systému](media/itsmc-connections/snow-system-oauth.png)
> 3. Klikněte na odvolat přístup a než na odvolat.

- Nainstalujte uživatelskou aplikaci pro Microsoft Log Analytics Integration (aplikace ServiceNow). [Přečtěte si další informace](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 ).
> [!NOTE]
> ITSMC podporuje pouze oficiální uživatelskou aplikaci pro integraci se službou Microsoft Log Analytics, která je stažena z úložiště ServiceNow. ITSMC nepodporují ingestování kódu na ServiceNow straně nebo v aplikaci, která není součástí oficiálního řešení ServiceNow. 
- Vytvoření role uživatele integrace pro uživatelskou aplikaci nainstalovanou. [Tady najdete](#create-integration-user-role-in-servicenow-app)informace o tom, jak vytvořit roli uživatele Integration.

### <a name="connection-procedure"></a>**Postup připojení**
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

Další informace: [vytvoření pracovních položek ITSM z výstrah Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts).


> [!NOTE]
> V ServiceNow platí omezení přenosové rychlosti pro žádosti za hodinu. Pokud chcete omezení nakonfigurovat, použijte k tomu definování "omezení rychlosti příchozího REST API" v instanci ServiceNow.

### <a name="create-integration-user-role-in-servicenow-app"></a>Vytvoření role uživatele integrace v aplikaci ServiceNow

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
> 
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
> 
> Konektor ITSM může odesílat incidenty do ServiceNow bez dalších modulů nainstalovaných v instanci ServiceNow. Pokud v instanci ServiceNow používáte modul EventManagement a chcete v ServiceNow vytvářet události nebo výstrahy pomocí konektoru, přidejte do uživatele integrace následující role:
> 
>    - evt_mgmt_integration
>    - evt_mgmt_operator  


## <a name="connect-provance-to-it-service-management-connector-in-azure"></a>Připojit inprov ke konektoru pro správu služeb IT v Azure

Následující části obsahují podrobné informace o tom, jak připojit produkt pro prokázání do ITSMC v Azure.

> [!NOTE]
> 
> Naši zákazníci s pořízením navrhují, aby používali [akci Webhooku](./action-groups.md#webhook) pro Cherwell a inprovcí koncový bod jako jiné řešení pro integraci.

### <a name="prerequisites"></a>Požadavky

Ujistěte se, že jsou splněné následující předpoklady:


- ITSMC je nainstalovaný. Další informace: [Přidání řešení IT Service Management Connector](./itsmc-definition.md#add-it-service-management-connector).
- Aplikace pro zakazování by měla být zaregistrovaná ve službě Azure AD – a ID klienta je dostupné. Podrobné informace najdete v tématu [Konfigurace ověřování služby Active Directory](../../app-service/configure-authentication-provider-aad.md).

- Role uživatele: správce.

### <a name="connection-procedure"></a>Postup připojení

Pomocí následujícího postupu vytvořte připojení k prokázání:

1. V Azure Portal přejít na **všechny prostředky** a vyhledejte **partnera (YourWorkspaceName)** .

2.  V části **zdroje dat pracovního prostoru** klikněte na **připojení ITSM**.
    ![Nové připojení](media/itsmc-connections/add-new-itsm-connection.png)

3. V horní části pravého podokna klikněte na **Přidat**.

4. Zadejte informace, jak je popsáno v následující tabulce, a kliknutím na tlačítko **OK** vytvořte připojení.

> [!NOTE]
> 
> Všechny tyto parametry jsou povinné.

| **Pole** | **Popis** |
| --- | --- |
| **Název připojení**   | Zadejte název instance pro prov, ke kterému se chcete připojit pomocí ITSMC.  Tento název použijete později při konfiguraci pracovních položek v tomto ITSM/zobrazení podrobné Log Analytics. |
| **Typ partnera**   | Vyberte **prov**. |
| **Uživatelské jméno**   | Zadejte uživatelské jméno, které se může připojit k ITSMC.    |
| **Heslo**   | Zadejte heslo přidružené k tomuto uživatelskému jménu. **Poznámka:** Uživatelské jméno a heslo slouží pouze k vytváření tokenů ověřování a nejsou uloženy kdekoli v rámci ITSMC service._|
| **Adresa URL serveru**   | Zadejte adresu URL vaší instance prov, ke které se chcete připojit ITSMC. |
| **ID klienta**   | Zadejte ID klienta pro ověřování tohoto připojení, které jste vygenerovali v instanci prokázání.  Další informace o ID klienta najdete v tématu [Konfigurace ověřování služby Active Directory](../../app-service/configure-authentication-provider-aad.md). |
| **Rozsah synchronizace dat**   | Pomocí ITSMC vyberte pracovní položky prov, které chcete synchronizovat s Azure Log Analytics.  Tyto pracovní položky se importují do Log Analytics.   **Možnosti:**   Incidenty, žádosti o změnu.|
| **Synchronizovat data** | Zadejte počet uplynulých dní, z nichž mají být data. **Maximální limit**: 120 dní. |
| **Vytvořit novou položku konfigurace v řešení ITSM** | Tuto možnost vyberte, pokud chcete vytvořit položky konfigurace v produktu ITSM. Pokud je tato možnost vybrána, ITSMC vytvoří v podporovaném systému ITSM v případě neexistujícího systému služby CI ovlivněné položky konfigurace. **Výchozí**: zakázáno.|

![Snímek obrazovky, který zvýrazní název připojení a seznam partnerských typů.](media/itsmc-connections/itsm-connections-provance-latest.png)

**Po úspěšném připojení a synchronizaci**:

- Vybrané pracovní položky z této instance prov se importují do Azure **Log Analytics.** Souhrn těchto pracovních položek můžete zobrazit na dlaždici konektoru pro IT služby IT.

- Můžete vytvářet incidenty z výstrah Log Analytics nebo ze záznamů protokolů nebo z výstrah Azure v této instanci prov.

Další informace: [vytvoření pracovních položek ITSM z výstrah Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

## <a name="connect-cherwell-to-it-service-management-connector-in-azure"></a>Připojení Cherwell ke konektoru pro správu služeb IT v Azure

Následující části obsahují podrobné informace o tom, jak připojit produkt Cherwell k ITSMC v Azure.

> [!NOTE]
> 
> Naši zákazníci v Cherwell navrhují použít [akci Webhooku](./action-groups.md#webhook) pro koncový bod Cherwell a prov jako jiné řešení pro integraci.

### <a name="prerequisites"></a>Požadavky

Ujistěte se, že jsou splněné následující předpoklady:

- ITSMC je nainstalovaný. Další informace: [Přidání řešení IT Service Management Connector](./itsmc-definition.md#add-it-service-management-connector).
- ID klienta bylo vygenerováno. Další informace: [vygenerujte ID klienta pro Cherwell](#generate-client-id-for-cherwell).
- Role uživatele: správce.

### <a name="connection-procedure"></a>Postup připojení

K vytvoření připojení Cherwell použijte následující postup:

1. V Azure Portal přejít na **všechny prostředky** a vyhledejte **partnera (YourWorkspaceName)** .

2.  V části **zdroje dat pracovního prostoru** klikněte na **připojení ITSM**.
    ![Nové připojení](media/itsmc-connections/add-new-itsm-connection.png)

3. V horní části pravého podokna klikněte na **Přidat**.

4. Zadejte informace, jak je popsáno v následující tabulce, a kliknutím na tlačítko **OK** vytvořte připojení.

> [!NOTE]
> 
> Všechny tyto parametry jsou povinné.

| **Pole** | **Popis** |
| --- | --- |
| **Název připojení**   | Zadejte název instance Cherwell, ke které se chcete připojit ITSMC.  Tento název použijete později při konfiguraci pracovních položek v tomto ITSM/zobrazení podrobné Log Analytics. |
| **Typ partnera**   | Vyberte **Cherwell.** |
| **Uživatelské jméno**   | Zadejte uživatelské jméno Cherwell, které se může připojit k ITSMC. |
| **Heslo**   | Zadejte heslo přidružené k tomuto uživatelskému jménu. **Poznámka:** Uživatelské jméno a heslo slouží pouze k vytváření tokenů ověřování a nejsou uloženy kdekoli v rámci služby ITSMC.|
| **Adresa URL serveru**   | Zadejte adresu URL instance Cherwell, ke které se chcete připojit ITSMC. |
| **ID klienta**   | Zadejte ID klienta pro ověřování tohoto připojení, které jste vygenerovali v instanci Cherwell.   |
| **Rozsah synchronizace dat**   | Vyberte Cherwell pracovní položky, které chcete synchronizovat prostřednictvím ITSMC.  Tyto pracovní položky se importují do Log Analytics.   **Možnosti:**  Incidenty, žádosti o změnu. |
| **Synchronizovat data** | Zadejte počet uplynulých dní, z nichž mají být data. **Maximální limit**: 120 dní. |
| **Vytvořit novou položku konfigurace v řešení ITSM** | Tuto možnost vyberte, pokud chcete vytvořit položky konfigurace v produktu ITSM. Pokud je tato možnost vybrána, ITSMC vytvoří v podporovaném systému ITSM v případě neexistujícího systému služby CI ovlivněné položky konfigurace. **Výchozí**: zakázáno. |


![Připojení k přikázání](media/itsmc-connections/itsm-connections-cherwell-latest.png)

**Po úspěšném připojení a synchronizaci**:

- Vybrané pracovní položky z této instance Cherwell se importují do Azure **Log Analytics.** Souhrn těchto pracovních položek můžete zobrazit na dlaždici konektoru pro IT služby IT.

- Můžete vytvářet incidenty z výstrah Log Analytics nebo ze záznamů protokolů nebo z výstrah Azure v této instanci Cherwell.

Další informace: [vytvoření pracovních položek ITSM z výstrah Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

### <a name="generate-client-id-for-cherwell"></a>Vygenerovat ID klienta pro Cherwell

K vygenerování ID klienta/klíče pro Cherwell použijte následující postup:

1. Přihlaste se ke své instanci Cherwell jako správce.
2. Klikněte na **zabezpečení**  >  **Upravit REST API nastavení klienta**.
3. Vyberte **vytvořit nový**  >  **tajný klíč** klienta klienta.

    ![ID uživatele Cherwell](media/itsmc-connections/itsmc-cherwell-client-id.png)

## <a name="next-steps"></a>Další kroky

[Přehled konektoru ITSM](itsmc-overview.md) [vytvořit pracovní položky ITSM z Azure výstrahy](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts) 
 [řešení potíží v konektoru ITSM](./itsmc-resync-servicenow.md)