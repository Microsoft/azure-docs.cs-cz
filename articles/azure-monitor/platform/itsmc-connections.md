---
title: V Azure Log Analytics podporována připojení IT Service Management Connector | Dokumentace Microsoftu
description: Tento článek obsahuje informace o tom, jak připojit pomocí konektoru pro správu služeb IT (ITSMC) ve službě Azure Monitor centrálně monitorovat a spravovat pracovní položky ITSM ITSM produktů a služeb.
documentationcenter: ''
author: jyothirmaisuri
manager: riyazp
editor: ''
ms.assetid: 8231b7ce-d67f-4237-afbf-465e2e397105
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: v-jysur
ms.component: ''
ms.openlocfilehash: a7b24ff3f51cdd66391f8dd7c73598530f767420
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52865586"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>Připojení ITSM produktů a služeb s IT Service Management Connector
Tento článek obsahuje informace o tom, jak nakonfigurovat připojení mezi ITSM produkt nebo službu a na IT Service Management Connector (ITSMC) ve službě Log Analytics a centrálně spravovat pracovní položky. Další informace o ITSMC najdete v tématu [přehled](../../azure-monitor/platform/itsmc-overview.md).

Jsou podporovány následující ITSM produktů a služeb. Vyberte produkt, chcete-li zobrazit podrobné informace o tom, jak se připojit k ITSMC produktu.

- [System Center Service Manager](#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](#connect-provance-to-it-service-management-connector-in-azure)
- [Cherwell](#connect-cherwell-to-it-service-management-connector-in-azure)

> [!NOTE]

> Konektor ITSM se můžete připojit jenom k instance ServiceNow založené na cloudu. Místní instance ServiceNow není momentálně podporované.

## <a name="connect-system-center-service-manager-to-it-service-management-connector-in-azure"></a>Připojení produktu System Center Service Manager k IT Service Management Connector v Azure

Následující části obsahují podrobnosti o tom, jak připojit svůj produkt System Center Service Manager k ITSMC v Azure.

### <a name="prerequisites"></a>Požadavky

Ujistěte se, že jsou splněné následující požadavky:

- ITSMC nainstalované. Další informace: [přidávání řešení IT Service Management Connector](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Nasazení a konfiguraci aplikace webového portálu Service Manager (webová aplikace). Informace o webové aplikace je [tady](#create-and-deploy-service-manager-web-app-service).
- Hybridní připojení, vytvoření a konfiguraci. Další informace: [konfigurace hybridní připojení](#configure-the-hybrid-connection).
- Podporované verze portálu Service Manager: 2012 R2 nebo 2016.
- Role uživatele: [pokročilý operátor](https://technet.microsoft.com/library/ff461054.aspx).

### <a name="connection-procedure"></a>Postup připojení

Pomocí následujícího postupu pro připojení k ITSMC instanci aplikace System Center Service Manager:

1. Na webu Azure portal, přejděte na **všechny prostředky** a hledejte **ServiceDesk(YourWorkspaceName)**

2.  V části **zdroje dat pracovního prostoru** klikněte na tlačítko **připojení ITSM**.

    ![Nové připojení](media/itsmc-connections/add-new-itsm-connection.png)

3. V horní části pravého podokna, klikněte na tlačítko **přidat**.

4. Zadejte informace, jak je popsáno v následující tabulce a klikněte na tlačítko **OK** k vytvoření připojení.

> [!NOTE]

> Všechny tyto parametry jsou povinné.

| **Pole** | **Popis** |
| --- | --- |
| **Název připojení**   | Zadejte název, který chcete připojit pomocí ITSMC instance System Center Service Manager.  Tento název použijete později při konfigurování pracovních položek v tomto případě / zobrazit podrobné log analytics. |
| **Typ partnera**   | Vyberte **System Center Service Manager**. |
| **Adresa URL serveru**   | Zadejte adresu URL aplikace webového portálu Service Manager. Další informace o aplikaci webového portálu Service Manager je [tady](#create-and-deploy-service-manager-web-app-service).
| **ID klienta**   | Zadejte ID klienta, který jste vygenerovali (pomocí automatického skriptu) pro ověřování webové aplikace. Další informace o automatizovaný skript je [tady.](../../azure-monitor/platform/itsmc-service-manager-script.md)|
| **Tajný klíč klienta**   | Zadejte tajný kód klienta vygenerovaný pro toto ID.   |
| **Rozsah synchronizace dat**   | Výběr pracovních položek portálu Service Manager, které chcete synchronizovat prostřednictvím ITSMC.  Tyto pracovní položky jsou importovány do Log Analytics. **Možnosti:** incidenty, žádosti o změnu.|
| **Synchronizace dat** | Zadejte počet uplynulých dní, které chcete data z. **Maximální limit**: 120 dnů. |
| **Vytvořit novou položku konfigurace v řešení ITSM** | Tuto možnost vyberte, pokud chcete vytvářet položky konfigurace v produktu ITSM. Pokud je vybráno, Log Analytics vytvoří ovlivněné položky konfigurace jako položky konfigurace (v případě neexistující CIs) v podporovaném systému ITSM. **Výchozí**: zakázáno. |

![Služba Správce připojení](media/itsmc-connections/service-manager-connection.png)

**Při úspěšném připojení a synchronizované**:

- Vybrané pracovní položky z portálu Service Manager jsou importovány do Azure **Log Analytics.** Můžete zobrazit souhrn těchto pracovních položek v dlaždici IT Service Management Connector.

- Můžete vytvořit incidenty, z upozornění Log Analytics nebo záznamy protokolu nebo upozornění v Azure v této instanci portálu Service Manager.


Další informace: [pracovní položky ITSM vytvořit z výstrah Azure](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-and-deploy-service-manager-web-app-service"></a>Vytvoření a nasazení portálu Service Manager web app service

Pro připojení k portálu Service Manager v místním pomocí ITSMC v Azure, společnost Microsoft vytvořila aplikace webového portálu Service Manager na Githubu.

Pokud chcete nastavit ITSM webovou aplikaci pro váš portál Service Manager, postupujte takto:

- **Nasazení webové aplikace** – nasazení webové aplikace, nastavte vlastnosti a ověření ve službě Azure AD. Můžete nasadit webové aplikace pomocí [automatizovaných skriptů](../../azure-monitor/platform/itsmc-service-manager-script.md) , Microsoft vám poskytuje.
- **Konfigurace hybridních připojení** - [konfigurace tohoto připojení](#configure-the-hybrid-connection), je nutné ručně.

#### <a name="deploy-the-web-app"></a>Nasazení webové aplikace
Použít automatizované [skript](../../azure-monitor/platform/itsmc-service-manager-script.md) k nasazení webové aplikace, nastavte vlastnosti a ověření ve službě Azure AD.

Spusťte skript tím, že poskytuje následující požadované podrobnosti:

- Podrobnosti předplatného Azure
- Název skupiny prostředků
- Umístění
- Podrobnosti serveru portálu Service Manager (název serveru, domény, uživatelské jméno a heslo)
- Předpona názvu webu pro webové aplikace
- ServiceBus Namespace.

Tento skript vytvoří webovou aplikaci pomocí názvu, který jste zadali (spolu s několika další řetězce pro zajištění jeho jedinečnosti). Generuje **adresa URL webové aplikace**, **ID klienta** a **tajný kód klienta**.

Uložit hodnoty můžete využít při vytváření připojení s ITSMC.

**Kontrola instalace webové aplikace**

1. Přejděte na **webu Azure portal** > **prostředky**.
2. Vyberte webovou aplikaci, klikněte na tlačítko **nastavení** > **nastavení aplikace**.
3. Zkontrolujte informace o instanci portálu Service Manager, které jste zadali při nasazení aplikace pomocí skriptu.

### <a name="configure-the-hybrid-connection"></a>Konfigurace hybridních připojení

Pomocí následujícího postupu ke konfiguraci hybridního připojení, která se připojuje instance portálu Service Manager s ITSMC v Azure.

1. Vyhledání aplikace webového portálu Service Manager v části **prostředky Azure**.
2. Klikněte na tlačítko **nastavení** > **sítě**.
3. V části **Hybrid Connections**, klikněte na tlačítko **konfigurovat vaše koncové body hybridního připojení**.

    ![Hybridní připojení sítě](media/itsmc-connections/itsmc-hybrid-connection-networking-and-end-points.png)
4. V **Hybrid Connections** okna, klikněte na tlačítko **přidat hybridní připojení**.

    ![Přidat hybridní připojení](media/itsmc-connections/itsmc-new-hybrid-connection-add.png)

5. V **přidat hybridní připojení** okna, klikněte na tlačítko **vytvořit nové hybridní připojení**.

    ![Nové hybridní připojení](media/itsmc-connections/itsmc-create-new-hybrid-connection.png)

6. Zadejte následující hodnoty:

    - **Název koncového bodu**: Zadejte název nové hybridní připojení.
    -  **Hostitel koncového bodu**: plně kvalifikovaný název domény serveru pro správu portálu Service Manager.
    - **Port koncového bodu**: Zadejte 5724
    - **Obor názvů služby Service Bus**: použití existujícího oboru názvů služby Service Bus nebo vytvořte novou.
    - **Umístění**: Vyberte umístění.
    -  **Název**: Zadejte název, který služby Service Bus, pokud jeho vytváření.

    ![Hodnoty hybridní připojení](media/itsmc-connections/itsmc-new-hybrid-connection-values.png)
6. Klikněte na tlačítko **OK** zavřete **vytvořit hybridní připojení** blade a začít vytvářet hybridní připojení.

    Po vytvoření hybridního připojení se zobrazí pod oknem.

7. Po vytvoření hybridního připojení, vyberte připojení a klikněte na tlačítko **přidat vybrané hybridní připojení**.

    ![Nové hybridní připojení](media/itsmc-connections/itsmc-new-hybrid-connection-added.png)

#### <a name="configure-the-listener-setup"></a>Konfigurace nastavení naslouchacího procesu

Pomocí následujícího postupu ke konfiguraci nastavení naslouchacího procesu pro hybridní připojení.

1. V **Hybrid Connections** okna, klikněte na tlačítko **stáhnout správce připojení** ho a nainstalujte na počítači, kde je spuštěná instance System Center Service Manager.

    Po dokončení instalace **uživatelského rozhraní správce hybridního připojení** možnost je k dispozici v rámci **Start** nabídky.

2. Klikněte na tlačítko **uživatelského rozhraní správce hybridního připojení** , zobrazí se výzva k zadání přihlašovacích údajů Azure.

3. Přihlaste se pomocí přihlašovacích údajů Azure a vyberte své předplatné, ve kterém byla vytvořena hybridní připojení.

4. Klikněte na **Uložit**.

Hybridní připojení se úspěšně připojil.

![úspěšné hybridní připojení](media/itsmc-connections/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]

> Po hybridního připojení vytvořit, ověřit a vyzkoušejte připojení návštěvou nasazené aplikace webového portálu Service Manager. Ujistěte se, že je připojení úspěšné, než se pokusíte připojit k ITSMC v Azure.

Následující ukázkový obrázek zobrazuje podrobnosti o úspěšném připojení:

![Test hybridní připojení](media/itsmc-connections/itsmc-hybrid-connection-test.png)

## <a name="connect-servicenow-to-it-service-management-connector-in-azure"></a>Připojení ServiceNow k IT Service Management Connector v Azure

Následující části obsahují podrobnosti o tom, jak připojit ServiceNow produkt ITSMC v Azure.

### <a name="prerequisites"></a>Požadavky
Ujistěte se, že jsou splněné následující požadavky:
- ITSMC nainstalované. Další informace: [přidávání řešení IT Service Management Connector](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- ServiceNow podporované verze: Kingston, Jakarta, Istanbul, Helsinky, Geneva.

**Správci ServiceNow musíte provést následující v jejich instance ServiceNow**:
- Vygenerujte ID klienta a tajný kód klienta ServiceNow produktu. Informace o tom, jak vygenerovat ID klienta a tajný kód naleznete následující informace podle potřeby:

    - [Nastavení OAuth pro Kingston](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Nastavení OAuth pro Jakarta](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Nastavení OAuth pro Istanbul](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Nastavení OAuth pro Helsinky](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [Nastavení OAuth pro Geneva](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)


- Nainstalujte si aplikaci uživatele pro integraci Microsoft Log Analytics (ServiceNow aplikace). [Další informace](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 ).
- Umožňuje vytvořte roli uživatele integrace pro nainstalovanou aplikaci uživatele. Informace o tom, jak vytvořit roli uživatele integrace [tady](#create-integration-user-role-in-servicenow-app).

### <a name="connection-procedure"></a>**Postup připojení**
Pomocí následujícího postupu vytvořte připojení ServiceNow:


1. Na webu Azure portal, přejděte na **všechny prostředky** a hledejte **ServiceDesk(YourWorkspaceName)**

2.  V části **zdroje dat pracovního prostoru** klikněte na tlačítko **připojení ITSM**.
    ![Nové připojení](media/itsmc-connections/add-new-itsm-connection.png)

3. V horní části pravého podokna, klikněte na tlačítko **přidat**.

4. Zadejte informace, jak je popsáno v následující tabulce a klikněte na tlačítko **OK** k vytvoření připojení.


> [!NOTE]
> Všechny tyto parametry jsou povinné.

| **Pole** | **Popis** |
| --- | --- |
| **Název připojení**   | Zadejte název instance ServiceNow, kterou chcete připojit pomocí ITSMC.  Tento název použijete později v Log Analytics při konfigurování pracovních položek v tomto ITSM / zobrazit podrobné log analytics. |
| **Typ partnera**   | Vyberte **ServiceNow**. |
| **Uživatelské jméno**   | Integrace uživatelské jméno, které jste vytvořili v aplikaci ServiceNow a podporují připojení k ITSMC typ. Další informace: [role uživatele vytvořit ServiceNow aplikace](#create-integration-user-role-in-servicenow-app).|
| **Heslo**   | Zadejte heslo přidružené k tomuto uživatelskému jménu. **Poznámka:**: uživatelské jméno a heslo slouží ke generování tokenů ověřování jenom a nejsou nikde uložené ve službě ITSMC.  |
| **Adresa URL serveru**   | Zadejte adresu URL instance ServiceNow, kterou chcete připojit k ITSMC. |
| **ID klienta**   | Zadejte ID klienta, který chcete použít pro ověřování OAuth2, který jste vygenerovali dříve.  Další informace o generování ID klienta a tajný klíč: [instalace OAuth](http://wiki.servicenow.com/index.php?title=OAuth_Setup). |
| **Tajný klíč klienta**   | Zadejte tajný kód klienta vygenerovaný pro toto ID.   |
| **Rozsah synchronizace dat**   | Vyberte pracovní položky ServiceNow, které chcete synchronizovat s Azure Log Analytics prostřednictvím ITSMC.  Vybrané hodnoty jsou importovány do log analytics.   **Možnosti:** incidenty a žádosti o změnu.|
| **Synchronizace dat** | Zadejte počet uplynulých dní, které chcete data z. **Maximální limit**: 120 dnů. |
| **Vytvořit novou položku konfigurace v řešení ITSM** | Tuto možnost vyberte, pokud chcete vytvářet položky konfigurace v produktu ITSM. Pokud je vybráno, ITSMC vytvoří ovlivněné položky konfigurace jako položky konfigurace (v případě neexistující CIs) v podporovaném systému ITSM. **Výchozí**: zakázáno. |

![Připojení ServiceNow](media/itsmc-connections/itsm-connection-servicenow-connection-latest.png)

**Při úspěšném připojení a synchronizované**:

- Vybrané pracovní položky z ServiceNow instance importují do Azure **Log Analytics.** Můžete zobrazit souhrn těchto pracovních položek v dlaždici IT Service Management Connector.

- Můžete vytvořit incidenty, z upozornění Log Analytics nebo záznamy protokolu nebo upozornění v Azure v této instanci ServiceNow.

Další informace: [pracovní položky ITSM vytvořit z výstrah Azure](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-integration-user-role-in-servicenow-app"></a>Vytvoření role uživatele integrace v aplikaci ServiceNow

Uživatel takto:

1.  Přejděte [ServiceNow úložiště](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) a nainstalujte **uživatele aplikace pro ServiceNow a integraci Microsoft OMS** do vaší Instance ServiceNow.
   
   >[!NOTE]
   >Jako součást probíhající přechod z Microsoft Operations Management Suite (OMS) do Azure monitoru OMS se teď označuje jako Log Analytics.     
2.  Po instalaci, přejděte v levém navigačním panelu ServiceNow instance, hledání a vybrat integrátor Microsoft OMS.  
3.  Klikněte na tlačítko **kontrolní seznam pro instalaci**.

    Stav se zobrazí jako **dokončena** Pokud roli uživatele ještě má být vytvořen.

4.  Do textových polí vedle **vytvořit integrace uživatele**, zadejte uživatelské jméno pro uživatele, který se může připojit k ITSMC v Azure.
5.  Zadejte heslo pro tohoto uživatele a klikněte na tlačítko **OK**.  

>[!NOTE]

> Tyto přihlašovací údaje použijete k vytvoření připojení ServiceNow v Azure.

Zobrazí se nově vytvořeného uživatele se výchozí role přiřazené.

**Výchozí role**:
- personalize_choices
- import_transformer
-   x_mioms_microsoft.user
-   ITIL
-   template_editor
-   view_changer

Jakmile uživatel je úspěšně vytvořen, stav **zkontrolujte kontrolní seznam pro instalaci** přesune na dokončeno, podrobnosti role uživatele vytvořit pro aplikaci.

> [!NOTE]

> ITSM konektor můžete odesílat incidenty ServiceNow bez dalších modulů, které mají nainstalovaný na instanci ServiceNow. Pokud používáte modul EventManagement ve vaší instanci ServiceNow a chcete k vytvoření události nebo výstrahy v ServiceNow s použitím konektoru, přidejte následující role integrace uživateli:

>    - evt_mgmt_integration
>    - evt_mgmt_operator  


## <a name="connect-provance-to-it-service-management-connector-in-azure"></a>Připojit Provance IT Service Management Connector v Azure

Následující části obsahují podrobnosti o tom, jak připojit svůj produkt Provance ITSMC v Azure.


### <a name="prerequisites"></a>Požadavky

Ujistěte se, že jsou splněné následující požadavky:


- ITSMC nainstalované. Další informace: [přidávání řešení IT Service Management Connector](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- S Azure AD – by měly být zaregistrovány aplikace provance a ID klienta je k dispozici. Podrobné informace najdete v tématu [ke konfiguraci ověřování služby active directory](../../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md).

- Role uživatele: správce.

### <a name="connection-procedure"></a>Postup připojení

Pomocí následujícího postupu vytvořte Provance připojení:

1. Na webu Azure portal, přejděte na **všechny prostředky** a hledejte **ServiceDesk(YourWorkspaceName)**

2.  V části **zdroje dat pracovního prostoru** klikněte na tlačítko **připojení ITSM**.
    ![Nové připojení](media/itsmc-connections/add-new-itsm-connection.png)

3. V horní části pravého podokna, klikněte na tlačítko **přidat**.

4. Zadejte informace, jak je popsáno v následující tabulce a klikněte na tlačítko **OK** k vytvoření připojení.

> [!NOTE]

> Všechny tyto parametry jsou povinné.

| **Pole** | **Popis** |
| --- | --- |
| **Název připojení**   | Zadejte název, který chcete připojit pomocí ITSMC Provance instance.  Tento název použijete později při konfigurování pracovních položek v tomto ITSM / zobrazit podrobné log analytics. |
| **Typ partnera**   | Vyberte **Provance**. |
| **Uživatelské jméno**   | Zadejte uživatelské jméno, které se můžou připojovat k ITSMC.    |
| **Heslo**   | Zadejte heslo přidružené k tomuto uživatelskému jménu. **Poznámka:** uživatelské jméno a heslo slouží ke generování tokenů ověřování jenom a nejsou nikde uložené ve službě ITSMC. _|
| **Adresa URL serveru**   | Zadejte adresu URL, kterou chcete připojit k ITSMC Provance instance. |
| **ID klienta**   | Zadejte ID klienta pro toto připojení, který jste vygenerovali v instanci Provance ověřování.  Další informace o ID klienta, viz [ke konfiguraci ověřování služby active directory](../../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md). |
| **Rozsah synchronizace dat**   | Vyberte Provance pracovní položky, které chcete synchronizovat s Azure Log Analytics prostřednictvím ITSMC.  Tyto pracovní položky jsou importovány do log analytics.   **Možnosti:** incidenty, žádosti o změnu.|
| **Synchronizace dat** | Zadejte počet uplynulých dní, které chcete data z. **Maximální limit**: 120 dnů. |
| **Vytvořit novou položku konfigurace v řešení ITSM** | Tuto možnost vyberte, pokud chcete vytvářet položky konfigurace v produktu ITSM. Pokud je vybráno, ITSMC vytvoří ovlivněné položky konfigurace jako položky konfigurace (v případě neexistující CIs) v podporovaném systému ITSM. **Výchozí**: zakázáno.|

![Provance připojení](media/itsmc-connections/itsm-connections-provance-latest.png)

**Při úspěšném připojení a synchronizované**:

- Vybrané pracovní položky z této instance Provance importují do Azure **Log Analytics.** Můžete zobrazit souhrn těchto pracovních položek v dlaždici IT Service Management Connector.

- Můžete vytvořit incidenty z upozornění Log Analytics nebo záznamy protokolu nebo v tomto případě Provance upozornění v Azure.

Další informace: [pracovní položky ITSM vytvořit z výstrah Azure](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

## <a name="connect-cherwell-to-it-service-management-connector-in-azure"></a>Připojit Cherwell IT Service Management Connector v Azure

Následující části obsahují podrobnosti o tom, jak připojit svůj produkt Cherwell ITSMC v Azure.

### <a name="prerequisites"></a>Požadavky

Ujistěte se, že jsou splněné následující požadavky:

- ITSMC nainstalované. Další informace: [přidávání řešení IT Service Management Connector](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution).
- Vygeneruje ID klienta. Další informace: [vygenerovat ID klienta pro Cherwell](#generate-client-id-for-cherwell).
- Role uživatele: správce.

### <a name="connection-procedure"></a>Postup připojení

Pomocí následujícího postupu vytvořte Provance připojení:

1. Na webu Azure portal, přejděte na **všechny prostředky** a hledejte **ServiceDesk(YourWorkspaceName)**

2.  V části **zdroje dat pracovního prostoru** klikněte na tlačítko **připojení ITSM**.
    ![Nové připojení](media/itsmc-connections/add-new-itsm-connection.png)

3. V horní části pravého podokna, klikněte na tlačítko **přidat**.

4. Zadejte informace, jak je popsáno v následující tabulce a klikněte na tlačítko **OK** k vytvoření připojení.

> [!NOTE]

> Všechny tyto parametry jsou povinné.

| **Pole** | **Popis** |
| --- | --- |
| **Název připojení**   | Zadejte název, který chcete připojit k ITSMC instance Cherwell.  Tento název použijete později při konfigurování pracovních položek v tomto ITSM / zobrazit podrobné log analytics. |
| **Typ partnera**   | Vyberte **Cherwell.** |
| **Uživatelské jméno**   | Zadejte uživatelské jméno Cherwell, který se může připojit k ITSMC. |
| **Heslo**   | Zadejte heslo přidružené k tomuto uživatelskému jménu. **Poznámka:** uživatelské jméno a heslo slouží ke generování tokenů ověřování jenom a nejsou nikde uložené ve službě ITSMC.|
| **Adresa URL serveru**   | Zadejte adresu URL, kterou chcete připojit k ITSMC instance Cherwell. |
| **ID klienta**   | Zadejte ID klienta pro toto připojení, který jste vygenerovali v instanci Cherwell ověřování.   |
| **Rozsah synchronizace dat**   | Vyberte Cherwell pracovní položky, které chcete synchronizovat prostřednictvím ITSMC.  Tyto pracovní položky jsou importovány do log analytics.   **Možnosti:** incidenty, žádosti o změnu. |
| **Synchronizace dat** | Zadejte počet uplynulých dní, které chcete data z. **Maximální limit**: 120 dnů. |
| **Vytvořit novou položku konfigurace v řešení ITSM** | Tuto možnost vyberte, pokud chcete vytvářet položky konfigurace v produktu ITSM. Pokud je vybráno, ITSMC vytvoří ovlivněné položky konfigurace jako položky konfigurace (v případě neexistující CIs) v podporovaném systému ITSM. **Výchozí**: zakázáno. |


![Provance připojení](media/itsmc-connections/itsm-connections-cherwell-latest.png)

**Při úspěšném připojení a synchronizované**:

- Vybrané pracovní položky z této instance Cherwell importují do Azure **Log Analytics.** Můžete zobrazit souhrn těchto pracovních položek v dlaždici IT Service Management Connector.

- Můžete vytvořit incidenty, z upozornění Log Analytics nebo záznamy protokolu nebo upozornění v Azure v tomto případě Cherwell.

Další informace: [pracovní položky ITSM vytvořit z výstrah Azure](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="generate-client-id-for-cherwell"></a>Vygenerovat ID klienta pro Cherwell

Generovat klíč/ID klienta pro Cherwell, postupujte takto:

1. Přihlaste se k vaší instanci Cherwell jako správce.
2. Klikněte na tlačítko **zabezpečení** > **nastavení klienta REST API upravit**.
3. Vyberte **vytvořit nového klienta** > **tajný kód klienta**.

    ![Cherwell id uživatele](media/itsmc-connections/itsmc-cherwell-client-id.png)


## <a name="next-steps"></a>Další postup
 - [Vytvoření pracovní položky ITSM z výstrah Azure](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts)
