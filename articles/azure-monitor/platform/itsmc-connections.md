---
title: Konektor správy služeb IT ve službě Azure Monitor
description: Tento článek obsahuje informace o tom, jak připojit produkty/služby ITSM ke konektoru ITSMC (IT Service Management Connector) v Azure Monitor ucentrálního monitorování a správy pracovních položek ITSM.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.openlocfilehash: 0773492c3042a6f8c906aa6ba1bc3c76ea8c0d8f
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870597"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>Propojení produktů/služeb ITSM pomocí konektoru pro správu služeb IT
Tento článek obsahuje informace o tom, jak nakonfigurovat připojení mezi produktem nebo službou ITSM a konektorem ITSMC (IT Service Management Connector) v Log Analytics pro centrální správu pracovních položek. Další informace o ITSMC naleznete v [tématu Přehled](../../azure-monitor/platform/itsmc-overview.md).

Jsou podporovány následující produkty/služby ITSM. Výběrem produktu zobrazíte podrobné informace o tom, jak produkt připojit k ITSMC.

- [Správce služeb systémového centra](#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](#connect-provance-to-it-service-management-connector-in-azure)
- [Cherwell](#connect-cherwell-to-it-service-management-connector-in-azure)

> [!NOTE]
> 
> Konektor ITSM se může připojit pouze ke cloudovým instancím ServiceNow. Místní instance ServiceNow nejsou aktuálně podporovány.

## <a name="connect-system-center-service-manager-to-it-service-management-connector-in-azure"></a>Připojení správce služeb System Center ke konektoru správy služeb IT v Azure

V následujících částech jsou uvedeny podrobnosti o tom, jak připojit produkt Správce služeb system center k ITSMC v Azure.

### <a name="prerequisites"></a>Požadované součásti

Ujistěte se, že jsou splněny následující požadavky:

- ITSMC nainstalován. Další informace: [Přidání řešení konektoru pro správu služeb IT](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Webová aplikace Správce služeb (webová aplikace) je nasazená a nakonfigurovaná. Informace o webové aplikaci jsou [zde](#create-and-deploy-service-manager-web-app-service).
- Hybridní připojení vytvořeno a nakonfigurováno. Další informace: [Konfigurace hybridního připojení](#configure-the-hybrid-connection).
- Podporované verze Správce služeb: 2012 R2 nebo 2016.
- Role uživatele: [Pokročilý operátor](https://technet.microsoft.com/library/ff461054.aspx).

### <a name="connection-procedure"></a>Postup připojení

Pomocí následujícího postupu připojte instanci Správce služeb system center k ITSMC:

1. Na webu Azure Portal přejděte na **Všechny prostředky** a vyhledejte **ServiceDesk(YourWorkspaceName).**

2.  V části **Zdroje dat pracovního prostoru** klikněte na připojení **ITSM**.

    ![Nové připojení](media/itsmc-connections/add-new-itsm-connection.png)

3. V horní části pravého podokna klikněte na **Přidat**.

4. Zadejte informace popsané v následující tabulce a klepnutím na **tlačítko OK** vytvořte připojení.

> [!NOTE]
> 
> Všechny tyto parametry jsou povinné.

| **Pole** | **Popis** |
| --- | --- |
| **Název připojení**   | Zadejte název instance Správce služeb system center, kterou chcete spojit s ITSMC.  Tento název použijete později při konfiguraci pracovních položek v této instanci/ zobrazit podrobné analýzy protokolu. |
| **Typ partnera**   | Vyberte **správce služeb systémového centra**. |
| **Adresa URL serveru**   | Zadejte adresu URL webové aplikace Správce služeb. Další informace o webové aplikaci Správce služeb naleznete [zde](#create-and-deploy-service-manager-web-app-service).
| **ID klienta**   | Zadejte ID klienta, které jste vygenerovali (pomocí automatického skriptu) pro ověření webové aplikace. Více informací o automatizovaném skriptu naleznete [zde.](../../azure-monitor/platform/itsmc-service-manager-script.md)|
| **Tajný klíč klienta**   | Zadejte tajný klíč klienta, který byl vygenerován pro toto ID.   |
| **Synchronizace dat**   | Vyberte pracovní položky Správce služeb, které chcete synchronizovat prostřednictvím ITSMC.  Tyto pracovní položky jsou importovány do analýzy protokolů. **Možnosti:**  Incidenty, žádosti o změnu.|
| **Obor synchronizace dat** | Zadejte počet posledních dnů, od kterých chcete data. **Maximální limit**: 120 dní. |
| **Vytvoření nové položky konfigurace v řešení ITSM** | Tuto možnost vyberte, pokud chcete vytvořit položky konfigurace v produktu ITSM. Pokud je tato možnost vybrána, služba Log Analytics vytvoří ovlivněné cis jako položky konfigurace (v případě neexistujících cis) v podporovaném systému ITSM. **Výchozí**: zakázáno. |

![Připojení správce služeb](media/itsmc-connections/service-manager-connection.png)

**Při úspěšném připojení a synchronizaci**:

- Vybrané pracovní položky ze Správce služeb se importují do Azure **Log Analytics.** Souhrn těchto pracovních položek můžete zobrazit na dlaždici Konektor pro správu služeb IT.

- Incidenty můžete vytvářet z výstrah Analýzy protokolů nebo ze záznamů protokolu nebo z výstrah Azure v této instanci Správce služeb.


Další informace: [Vytvoření pracovních položek ITSM z výstrah Azure](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-and-deploy-service-manager-web-app-service"></a>Vytvoření a nasazení služby webových aplikací Správce služeb

Aby microsoft mohl propojit místnísprávce služeb s ITSMC v Azure, vytvořil na GitHubu webovou aplikaci Správce služeb.

Pokud chcete nastavit webovou aplikaci ITSM pro Správce služeb, postupujte takto:

- **Nasazení webové aplikace** – nasazení webové aplikace, nastavení vlastností a ověření pomocí Služby Azure AD. Webovou aplikaci můžete nasadit pomocí [automatického skriptu,](../../azure-monitor/platform/itsmc-service-manager-script.md) který vám společnost Microsoft poskytla.
- **Nakonfigurujte hybridní připojení** - [Nakonfigurujte toto připojení](#configure-the-hybrid-connection)ručně.

#### <a name="deploy-the-web-app"></a>Nasazení webové aplikace
Pomocí automatického [skriptu](../../azure-monitor/platform/itsmc-service-manager-script.md) nasadit webovou aplikaci, nastavit vlastnosti a ověřit pomocí Azure AD.

Spusťte skript zadáním následujících požadovaných podrobností:

- Podrobnosti o předplatném Azure
- Název skupiny prostředků
- Umístění
- Podrobnosti o serveru Správce služeb (název serveru, doména, uživatelské jméno a heslo)
- Předpona názvu webu pro webovou aplikaci
- Obor názvů servicebusu.

Skript vytvoří webovou aplikaci pomocí zadaného názvu (spolu s několika dalšími řetězci, aby byla jedinečná). Generuje adresu **URL webové aplikace**, **ID klienta** a **tajný klíč klienta**.

Uložte hodnoty, můžete je použít při vytváření spojení s ITSMC.

**Kontrola instalace webové aplikace**

1. Přejděte na > **prostředky portálu** **Azure**.
2. Vyberte webovou aplikaci a klepněte na **tlačítko Nastavení** > **aplikace**Nastavení .
3. Potvrďte informace o instanci Správce služeb, které jste zadali v době nasazení aplikace prostřednictvím skriptu.

### <a name="configure-the-hybrid-connection"></a>Konfigurace hybridního připojení

Pomocí následujícího postupu nakonfigurujte hybridní připojení, které spojuje instanci Správce služeb s ITSMC v Azure.

1. Najděte webovou aplikaci Správce služeb v části **Prostředky Azure**.
2. Klepněte na **položku Nastavení** > **sítě**.
3. V části **Hybridní připojení**klepněte na **položku Konfigurovat koncové body hybridního připojení**.

    ![Hybridní připojení sítě](media/itsmc-connections/itsmc-hybrid-connection-networking-and-end-points.png)
4. V okně **Hybridní připojení** klepněte na tlačítko Přidat **hybridní připojení**.

    ![Přidání hybridního připojení](media/itsmc-connections/itsmc-new-hybrid-connection-add.png)

5. V okně **Přidat hybridní připojení** klepněte na tlačítko Vytvořit nové hybridní **připojení**.

    ![Nové hybridní připojení](media/itsmc-connections/itsmc-create-new-hybrid-connection.png)

6. Zadejte následující hodnoty:

   - **Název koncového bodu**: Zadejte název nového hybridního připojení.
   - **Hostitel koncového bodu**: Hlavní název souboru souborů Na serveru pro správu správce služeb.
   - **Port koncového bodu**: Typ 5724
   - **Obor názvů ServiceBus**: Použijte existující obor názvů servicebus nebo vytvořte nový.
   - **Umístění**: vyberte umístění.
   - **Název**: Zadejte název servicebus, pokud ji vytváříte.

     ![Hodnoty hybridního připojení](media/itsmc-connections/itsmc-new-hybrid-connection-values.png)
6. Klepnutím na **tlačítko OK** zavřete okno Vytvořit **hybridní připojení** a začněte vytvářet hybridní připojení.

    Po vytvoření hybridního připojení se zobrazí pod nožem.

7. Po vytvoření hybridního připojení vyberte připojení a klepněte na tlačítko **Přidat vybrané hybridní připojení**.

    ![Nové hybridní připojení](media/itsmc-connections/itsmc-new-hybrid-connection-added.png)

#### <a name="configure-the-listener-setup"></a>Konfigurace nastavení posluchače

Pomocí následujícího postupu nakonfigurujte nastavení posluchače pro hybridní připojení.

1. V okně **Hybridní připojení** klikněte na Stáhnout **Správce připojení** a nainstalujte jej do počítače, ve kterém je spuštěna instance Správce služeb system center.

    Po dokončení instalace je v nabídce **Start** k dispozici možnost **ui nástroje Hybrid Connection Manager.**

2. Klikněte na **ui nástroje Hybrid Connection Manager** , budete vyzváni k zadání přihlašovacích údajů k Azure.

3. Přihlaste se pomocí přihlašovacích údajů Azure a vyberte předplatné, kde bylo vytvořeno hybridní připojení.

4. Klikněte na **Uložit**.

Hybridní připojení je úspěšně připojeno.

![úspěšné hybridní připojení](media/itsmc-connections/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]
> 
> Po vytvoření hybridního připojení ověřte a otestujte připojení nasazené webové aplikace Správce služeb. Ujistěte se, že připojení je úspěšné, než se pokusíte připojit k ITSMC v Azure.

Následující ukázkový obrázek znázorňuje podrobnosti o úspěšném připojení:

![Test hybridního připojení](media/itsmc-connections/itsmc-hybrid-connection-test.png)

## <a name="connect-servicenow-to-it-service-management-connector-in-azure"></a>Připojení servicenow ke konektoru správy služeb IT v Azure

V následujících částech jsou uvedeny podrobnosti o tom, jak připojit produkt ServiceNow k ITSMC v Azure.

### <a name="prerequisites"></a>Požadované součásti
Ujistěte se, že jsou splněny následující požadavky:
- ITSMC nainstalován. Další informace: [Přidání řešení konektoru pro správu služeb IT](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- ServiceNow podporované verze: New York, Madrid, Londýn, Kingston, Jakarta, Istanbul, Helsinky, Ženeva.
> [!NOTE]
> ITSMC podporuje pouze oficiální nabídku SaaS od Service Now. Privátní nasazení služby nyní nejsou podporovány. 

**Správci služby ServiceNow musí v instanci ServiceNow provést následující**kroky :
- Vygenerujte ID klienta a tajný klíč klienta pro produkt ServiceNow. Informace o tom, jak generovat ID klienta a tajný klíč, naleznete v následujících informacích:

    - [Nastavit OAuth pro New York](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Nastavit OAuth pro Madrid](https://docs.servicenow.com/bundle/madrid-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Nastavit OAuth pro Londýn](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Nastavit OAuth pro Kingston](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Nastavit OAuth pro Jakartu](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Nastavit OAuth pro Istanbul](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Nastavit OAuth pro Helsinky](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Nastavte OAuth pro Ženevu](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)
> [!NOTE]
> Jako součást definice "Nastavit OAuth" doporučujeme:
>
> 1) **Aktualizujte životnost obnovovacího tokenu na 90 dní (7 776 000 sekund):** Jako součást [Nastavit OAuth](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.servicenow.com%2Fbundle%2Fnewyork-platform-administration%2Fpage%2Fadminister%2Fsecurity%2Ftask%2Ft_SettingUpOAuth.html&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C2c6812e429a549e71cdd08d7d1b148d8%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637208431696739125&sdata=Q7mF6Ej8MCupKaEJpabTM56EDZ1T8vFVyihhoM594aA%3D&reserved=0) ve fázi 2: [Vytvořte koncový bod pro klienty pro přístup k instanci](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.servicenow.com%2Fbundle%2Fnewyork-platform-administration%2Fpage%2Fadminister%2Fsecurity%2Ftask%2Ft_CreateEndpointforExternalClients.html&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C2c6812e429a549e71cdd08d7d1b148d8%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637208431696749123&sdata=hoAJHJAFgUeszYCX1Q%2FXr4N%2FAKiFcm5WV7mwR2UqeWA%3D&reserved=0) Po definici koncového bodu v ServiceNow okno hledání systému OAuth než vyberte registr aplikací. Vyberte název OAuth, který byl definován a aktualizujte pole Aktualizovat token Životnost na 7 776 000 (90 dní v sekundách).
> Na konci klikněte na aktualizaci.
> 2) **Doporučujeme vytvořit vnitřní postup, aby se zajistilo, že spojení zůstane naživu:** Podle aktualizace tokenu Životnost aktualizovat token. Ujistěte se, že provést následující operace před obnovení token očekávané doby vypršení platnosti (Několik dní před vypršením životnosti obnovovací token doporučujeme):
>
>>  1) [Dokončení procesu ruční synchronizace pro konfiguraci konektoru ITSM](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-resync-servicenow)
 >> 2) Odvolat na starý obnovovací token, protože se nedoporučuje zachovat staré klíče z bezpečnostních důvodů. V servicenow okno hledání systému OAuth než vyberte Spravovat tokeny. Vyberte starý token ze seznamu podle názvu OAuth a data vypršení platnosti. Klikněte na Odvolat přístup a než na Odvolat.

- Nainstalujte uživatelskou aplikaci pro integraci Microsoft Log Analytics (aplikace ServiceNow). [Další informace](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 ).
- Vytvořte roli uživatele integrace pro nainstalovanou uživatelskou aplikaci. Informace o tom, jak vytvořit roli integračního [uživatele,](#create-integration-user-role-in-servicenow-app)naleznete zde .

### <a name="connection-procedure"></a>**Postup připojení**
K vytvoření připojení ServiceNow použijte následující postup:


1. Na webu Azure Portal přejděte na **Všechny prostředky** a vyhledejte **ServiceDesk(YourWorkspaceName).**

2.  V části **Zdroje dat pracovního prostoru** klikněte na připojení **ITSM**.
    ![Nové připojení](media/itsmc-connections/add-new-itsm-connection.png)

3. V horní části pravého podokna klikněte na **Přidat**.

4. Zadejte informace popsané v následující tabulce a klepnutím na **tlačítko OK** vytvořte připojení.


> [!NOTE]
> Všechny tyto parametry jsou povinné.

| **Pole** | **Popis** |
| --- | --- |
| **Název připojení**   | Zadejte název instance ServiceNow, kterou chcete připojit k ITSMC.  Tento název použijete později v Log Analytics při konfiguraci pracovních položek v tomto ITSM/ zobrazit podrobné analýzy protokolu. |
| **Typ partnera**   | Vyberte **servicenow**. |
| **Username**   | Zadejte uživatelské jméno integrace, které jste vytvořili v aplikaci ServiceNow pro podporu připojení k ITSMC. Další informace: [Vytvoření role uživatele aplikace ServiceNow](#create-integration-user-role-in-servicenow-app).|
| **Heslo**   | Zadejte heslo přidružené k tomuto uživatelskému jménu. **Poznámka:** Uživatelské jméno a heslo se používají pouze pro generování ověřovacích tokenů a nejsou uloženy nikde v rámci služby ITSMC.  |
| **Adresa URL serveru**   | Zadejte adresu URL instance ServiceNow, kterou chcete připojit k ITSMC. Adresa URL by měla ukazovat na podporovanou verzi SaaS s příponou ".servicenow.com".|
| **ID klienta**   | Zadejte ID klienta, které chcete použít pro ověřování OAuth2, které jste vygenerovali dříve.  Další informace o generování ID klienta a tajného klíče: [OAuth Setup](https://wiki.servicenow.com/index.php?title=OAuth_Setup). |
| **Tajný klíč klienta**   | Zadejte tajný klíč klienta, který byl vygenerován pro toto ID.   |
| **Obor synchronizace dat**   | Vyberte pracovní položky ServiceNow, které chcete synchronizovat s Azure Log Analytics, prostřednictvím ITSMC.  Vybrané hodnoty jsou importovány do analýzy protokolů.   **Možnosti:**  Incidenty a žádosti o změnu.|
| **Synchronizace dat** | Zadejte počet posledních dnů, od kterých chcete data. **Maximální limit**: 120 dní. |
| **Vytvoření nové položky konfigurace v řešení ITSM** | Tuto možnost vyberte, pokud chcete vytvořit položky konfigurace v produktu ITSM. Pokud je tato možnost vybrána, ITSMC vytvoří ovlivněné cis jako položky konfigurace (v případě neexistujících cis) v podporovaném systému ITSM. **Výchozí**: zakázáno. |

![ServiceNow připojení](media/itsmc-connections/itsm-connection-servicenow-connection-latest.png)

**Při úspěšném připojení a synchronizaci**:

- Vybrané pracovní položky z instance ServiceNow se importují do Azure **Log Analytics.** Souhrn těchto pracovních položek můžete zobrazit na dlaždici Konektor pro správu služeb IT.

- Incidenty můžete vytvářet z výstrah analýzy protokolů nebo ze záznamů protokolu nebo z výstrah Azure v této instanci ServiceNow.

Další informace: [Vytvoření pracovních položek ITSM z výstrah Azure](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-integration-user-role-in-servicenow-app"></a>Vytvoření role uživatele integrace v aplikaci ServiceNow

Uživatel následující postup:

1. Navštivte [servicenow úložiště](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) a nainstalovat **uživatelskou aplikaci pro ServiceNow a Microsoft OMS integrace** do servicenow instance.
   
   >[!NOTE]
   >V rámci probíhajícího přechodu z Microsoft Operations Management Suite (OMS) na Azure Monitor se oms teď označuje jako Log Analytics.     
2. Po instalaci navštivte levý navigační panel instance ServiceNow, vyhledejte a vyberte integrátor Microsoft OMS.  
3. Klepněte na **položku Kontrolní seznam instalace**.

   Stav se zobrazí jako **Není dokončeno,** pokud je ještě třeba vytvořit roli uživatele.

4. Do textových polí vedle **možnosti Vytvořit uživatele integrace**zadejte uživatelské jméno uživatele, který se může připojit k ITSMC v Azure.
5. Zadejte heslo pro tohoto uživatele a klepněte na tlačítko **OK**.  

> [!NOTE]
> 
> Tato pověření se používají k připojení ServiceNow v Azure.

Nově vytvořený uživatel se zobrazí s přiřazenými výchozími rolemi.

**Výchozí role**:
- personalize_choices
- import_transformer
-   x_mioms_microsoft.uživatel
-   Itil
-   template_editor
-   view_changer

Po úspěšném vytvoření uživatele se stav **kontrolního seznamu instalace kontroly kontroly přejde** na Dokončeno a zobrazí podrobnosti o roli uživatele vytvořené pro aplikaci.

> [!NOTE]
> 
> Konektor ITSM může odesílat incidenty servicenow bez jiných modulů nainstalovaných v instanci ServiceNow. Pokud používáte modul EventManagement v instanci ServiceNow a chcete vytvořit události nebo výstrahy v ServiceNow pomocí konektoru, přidejte uživateli integrace následující role:
> 
>    - evt_mgmt_integration
>    - evt_mgmt_operator  


## <a name="connect-provance-to-it-service-management-connector-in-azure"></a>Připojení provance ke konektoru správy služeb IT v Azure

V následujících částech jsou uvedeny podrobnosti o tom, jak propojit produkt s ITSMC v Azure.


### <a name="prerequisites"></a>Požadované součásti

Ujistěte se, že jsou splněny následující požadavky:


- ITSMC nainstalován. Další informace: [Přidání řešení konektoru pro správu služeb IT](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Aplikace provance by měla být registrovaná ve službě Azure AD – a ID klienta je k dispozici. Podrobné informace naleznete v tématu [Konfigurace ověřování služby Active Directory](../../app-service/configure-authentication-provider-aad.md).

- Role uživatele: Správce.

### <a name="connection-procedure"></a>Postup připojení

K vytvoření připojení Provance použijte následující postup:

1. Na webu Azure Portal přejděte na **Všechny prostředky** a vyhledejte **ServiceDesk(YourWorkspaceName).**

2.  V části **Zdroje dat pracovního prostoru** klikněte na připojení **ITSM**.
    ![Nové připojení](media/itsmc-connections/add-new-itsm-connection.png)

3. V horní části pravého podokna klikněte na **Přidat**.

4. Zadejte informace popsané v následující tabulce a klepnutím na **tlačítko OK** vytvořte připojení.

> [!NOTE]
> 
> Všechny tyto parametry jsou povinné.

| **Pole** | **Popis** |
| --- | --- |
| **Název připojení**   | Zadejte název instance Provance, kterou chcete spojit s ITSMC.  Tento název použijete později při konfiguraci pracovních položek v této analýze podrobného protokolu ITSM/ zobrazení. |
| **Typ partnera**   | Vyberte **možnost Provance**. |
| **Username**   | Zadejte uživatelské jméno, které se může připojit k ITSMC.    |
| **Heslo**   | Zadejte heslo přidružené k tomuto uživatelskému jménu. **Poznámka:** Uživatelské jméno a heslo se používají pouze pro generování ověřovacích tokenů a nejsou uloženy nikde v rámci služby ITSMC._|
| **Adresa URL serveru**   | Zadejte adresu URL instance Provance, kterou chcete připojit k ITSMC. |
| **ID klienta**   | Zadejte ID klienta pro ověření tohoto připojení, které jste vygenerovali v instanci Provance.  Další informace o ID klienta naleznete v [tématu konfigurace ověřování služby Active Directory](../../app-service/configure-authentication-provider-aad.md). |
| **Obor synchronizace dat**   | Vyberte pracovní položky Provance, které chcete synchronizovat s Azure Log Analytics, prostřednictvím ITSMC.  Tyto pracovní položky jsou importovány do analýzy protokolů.   **Možnosti:**   Incidenty, žádosti o změnu.|
| **Synchronizace dat** | Zadejte počet posledních dnů, od kterých chcete data. **Maximální limit**: 120 dní. |
| **Vytvoření nové položky konfigurace v řešení ITSM** | Tuto možnost vyberte, pokud chcete vytvořit položky konfigurace v produktu ITSM. Pokud je tato možnost vybrána, ITSMC vytvoří ovlivněné cis jako položky konfigurace (v případě neexistujících cis) v podporovaném systému ITSM. **Výchozí**: zakázáno.|

![Provance připojení](media/itsmc-connections/itsm-connections-provance-latest.png)

**Při úspěšném připojení a synchronizaci**:

- Vybrané pracovní položky z této instance Provance se importují do Azure **Log Analytics.** Souhrn těchto pracovních položek můžete zobrazit na dlaždici Konektor pro správu služeb IT.

- Incidenty můžete vytvářet z výstrah analýzy protokolů nebo ze záznamů protokolu nebo z výstrah Azure v této instanci Provance.

Další informace: [Vytvoření pracovních položek ITSM z výstrah Azure](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

## <a name="connect-cherwell-to-it-service-management-connector-in-azure"></a>Připojení cherwellu ke konektoru pro správu služeb IT v Azure

V následujících částech jsou uvedeny podrobnosti o tom, jak připojit produkt Cherwell k ITSMC v Azure.

### <a name="prerequisites"></a>Požadované součásti

Ujistěte se, že jsou splněny následující požadavky:

- ITSMC nainstalován. Další informace: [Přidání řešení konektoru pro správu služeb IT](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- ID klienta bylo vygenerováno. Další informace: [Vygenerujte ID klienta pro Cherwell](#generate-client-id-for-cherwell).
- Role uživatele: Správce.

### <a name="connection-procedure"></a>Postup připojení

K vytvoření připojení Provance použijte následující postup:

1. Na webu Azure Portal přejděte na **Všechny prostředky** a vyhledejte **ServiceDesk(YourWorkspaceName).**

2.  V části **Zdroje dat pracovního prostoru** klikněte na připojení **ITSM**.
    ![Nové připojení](media/itsmc-connections/add-new-itsm-connection.png)

3. V horní části pravého podokna klikněte na **Přidat**.

4. Zadejte informace popsané v následující tabulce a klepnutím na **tlačítko OK** vytvořte připojení.

> [!NOTE]
> 
> Všechny tyto parametry jsou povinné.

| **Pole** | **Popis** |
| --- | --- |
| **Název připojení**   | Zadejte název instance Cherwell, kterou chcete připojit k ITSMC.  Tento název použijete později při konfiguraci pracovních položek v této analýze podrobného protokolu ITSM/ zobrazení. |
| **Typ partnera**   | Vyberte **Cherwell.** |
| **Username**   | Zadejte uživatelské jméno Cherwell, které se může připojit k ITSMC. |
| **Heslo**   | Zadejte heslo přidružené k tomuto uživatelskému jménu. **Poznámka:** Uživatelské jméno a heslo se používají pouze pro generování ověřovacích tokenů a nejsou uloženy nikde v rámci služby ITSMC.|
| **Adresa URL serveru**   | Zadejte adresu URL instance Cherwell, kterou chcete připojit k ITSMC. |
| **ID klienta**   | Zadejte ID klienta pro ověření tohoto připojení, které jste vygenerovali v instanci Cherwell.   |
| **Obor synchronizace dat**   | Vyberte pracovní položky Cherwell, které chcete synchronizovat prostřednictvím ITSMC.  Tyto pracovní položky jsou importovány do analýzy protokolů.   **Možnosti:**  Incidenty, žádosti o změnu. |
| **Synchronizace dat** | Zadejte počet posledních dnů, od kterých chcete data. **Maximální limit**: 120 dní. |
| **Vytvoření nové položky konfigurace v řešení ITSM** | Tuto možnost vyberte, pokud chcete vytvořit položky konfigurace v produktu ITSM. Pokud je tato možnost vybrána, ITSMC vytvoří ovlivněné cis jako položky konfigurace (v případě neexistujících cis) v podporovaném systému ITSM. **Výchozí**: zakázáno. |


![Provance připojení](media/itsmc-connections/itsm-connections-cherwell-latest.png)

**Při úspěšném připojení a synchronizaci**:

- Vybrané pracovní položky z této instance Cherwell se importují do Azure **Log Analytics.** Souhrn těchto pracovních položek můžete zobrazit na dlaždici Konektor pro správu služeb IT.

- Můžete vytvářet incidenty z výstrah y Log Analytics nebo ze záznamů protokolu nebo z výstrah Azure v této instanci Cherwell.

Další informace: [Vytvoření pracovních položek ITSM z výstrah Azure](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="generate-client-id-for-cherwell"></a>Generovat ID klienta pro Cherwell

Chcete-li generovat ID klienta/klíč pro Cherwell, použijte následující postup:

1. Přihlaste se do své instance Cherwell jako správce.
2. Klepněte na tlačítko **Upravit** > **zabezpečení nastavení klienta rozhraní REST .**
3. Vyberte možnost Vytvořit nový tajný klíč **klienta** > **client secret**.

    ![Id uživatele Cherwell](media/itsmc-connections/itsmc-cherwell-client-id.png)


## <a name="next-steps"></a>Další kroky
 - [Vytvoření pracovních položek ITSM z výstrah Azure](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts)
