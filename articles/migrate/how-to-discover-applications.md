---
title: Vyhledávání aplikací na místních serverech pomocí Azure Migrate
description: Naučte se zjišťovat aplikace, role a funkce na místních serverech pomocí vyhodnocení Azure Migrate serveru.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/10/2020
ms.openlocfilehash: 8266b585881546b37bbb21b82780ab26d85dada7
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048076"
---
# <a name="discover-installed-applications-roles-and-features-software-inventory-and-sql-server-instances-and-databases"></a>Zjišťování nainstalovaných aplikací, rolí a funkcí (inventář softwaru) a instancí SQL Server a databází

Tento článek popisuje, jak zjišťovat nainstalované aplikace, role a funkce (inventář softwaru) a SQL Server instance a databáze na serverech, na kterých běží v prostředí VMware, pomocí Azure Migrate: Nástroj pro vyhodnocení serveru.

Provádění inventáře softwaru pomáhá identifikovat a přizpůsobit cestu migrace do Azure pro vaše úlohy. Inventář softwaru používá zařízení Azure Migrate k provádění zjišťování pomocí přihlašovacích údajů serveru. Je to zcela bez agenta – na servery nejsou nainstalované žádné agenty, aby bylo možné tato data shromažďovat.

> [!NOTE]
> Inventář softwaru je momentálně ve verzi Preview pro servery, na kterých běží jenom prostředí VMware, a je omezený jenom na zjišťování. V současné době nenabízíme posuzování na základě aplikací.<br/> Zjišťování a hodnocení instancí SQL Server a databází spuštěných ve vašem prostředí VMware je teď ve verzi Preview. Chcete-li vyzkoušet tuto funkci, použijte [**Tento odkaz**](https://aka.ms/AzureMigrate/SQL) k vytvoření projektu v oblasti **Austrálie – východ** . Pokud projekt již máte v Austrálii – východ a chcete si vyzkoušet tuto funkci, ujistěte se, že jste tyto [**požadavky**](how-to-discover-sql-existing-project.md) dokončili na portálu.

## <a name="before-you-start"></a>Než začnete

- Ujistěte se, že jste [vytvořili Azure Migrate projekt](./create-manage-projects.md) pomocí Azure Migrate: Nástroj pro vyhodnocení serveru byl do něj přidán.
- Projděte si [požadavky VMware](migrate-support-matrix-vmware.md#vmware-requirements) a proveďte inventarizaci softwaru.
- Před nastavením zařízení zkontrolujte [požadavky na zařízení](migrate-support-matrix-vmware.md#azure-migrate-appliance-requirements) .
- Před zahájením inventáře softwaru na serverech zkontrolujte [požadavky na zjišťování aplikací](migrate-support-matrix-vmware.md#application-discovery-requirements) .

## <a name="deploy-and-configure-the-azure-migrate-appliance"></a>Nasazení a konfigurace zařízení Azure Migrate

1. [Projděte si](migrate-appliance.md#appliance---vmware) požadavky na nasazení zařízení Azure Migrate.
2. Zkontrolujte adresy URL Azure, které bude zařízení potřebovat pro přístup k [veřejným](migrate-appliance.md#public-cloud-urls) a [státním cloudům](migrate-appliance.md#government-cloud-urls).
3. [Zkontrolujte data](migrate-appliance.md#collected-data---vmware) , která zařízení shromažďuje během zjišťování a posouzení.
4. [Poznamenejte si](migrate-support-matrix-vmware.md#port-access-requirements) požadavky na přístup k portu pro dané zařízení.
5. [Nasaďte zařízení Azure Migrate](how-to-set-up-appliance-vmware.md) a spusťte zjišťování. Chcete-li nasadit zařízení, Stáhněte a importujte šablonu vajíček do VMware a vytvořte na svém vCenter Server Server se systémem. Po nasazení zařízení je potřeba ho zaregistrovat v projektu Azure Migrate a nakonfigurovat ho tak, aby zahájil zjišťování.
6. Při konfiguraci zařízení musíte v nástroji Configuration Manager pro zařízení zadat následující:
    - Podrobnosti vCenter Server, ke kterým se chcete připojit
    - vCenter Server rozsah přihlašovacích údajů, aby se zjistily servery ve vašem prostředí VMware.
    - Přihlašovací údaje serveru, které můžou být přihlašovacími údaji k doméně/Windows (ne k doméně)/Linux (nedoménám). [Přečtěte si další informace](add-server-credentials.md) o tom, jak zadat přihlašovací údaje a jak je zpracovat.

## <a name="verify-permissions"></a>Ověření oprávnění

- Pro zjišťování a hodnocení musíte [vytvořit účet vCenter Server jen pro čtení](./tutorial-discover-vmware.md#prepare-vmware) . Účet jen pro čtení potřebuje oprávnění povolená pro **Virtual Machines**  >  **operace hostů**, aby bylo možné komunikovat se servery nástroje k provádění inventáře softwaru.
- Do Správce konfigurace zařízení pro zjišťování aplikací můžete přidat více přihlašovacích údajů k doméně a nedoménám (Windows/Linux). Pro všechny servery se systémem Linux potřebujete uživatelský účet hosta pro servery s Windows a běžný/normální uživatelský účet (přístup bez sudo). [Přečtěte si další informace](add-server-credentials.md) o tom, jak zadat přihlašovací údaje a jak je zpracovat.

### <a name="add-credentials-and-initiate-discovery"></a>Přidat pověření a iniciovat zjišťování

1. Otevřete Správce konfigurace zařízení, proveďte kontrolu požadovaných součástí a registraci zařízení.
2. Přejděte na panel **Správa přihlašovacích údajů a zdrojů zjišťování** .
1.  V **kroku 1: zadání přihlašovacích údajů vCenter Server** klikněte na **Přidat přihlašovací údaje** a zadejte přihlašovací údaje pro účet vCenter Server, který zařízení použije ke zjišťování serverů běžících na vCenter Server.
1. V **kroku 2: zadejte vCenter Server podrobnosti**, klikněte na **Přidat zdroj zjišťování** a vyberte popisný název přihlašovacích údajů z rozevíracího seznamu, zadejte **IP adresu nebo plně kvalifikovaný název domény** vCenter Serverho :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="panelu instance 3 v nástroji Configuration Manager pro vCenter Server podrobnosti"::: .
1. V **kroku 3: zadání přihlašovacích údajů serveru pro inventarizaci softwaru, analýzu závislostí bez agentů a zjišťování instancí SQL Server a databází**, klikněte na **Přidat přihlašovací údaje** a zadejte více přihlašovacích údajů serveru pro zahájení inventáře softwaru.
1. Kliknutím na **Spustit zjišťování Zahajte** vCenter Server zjišťování.

 Po dokončení zjišťování vCenter Server zařízení zahájí zjišťování nainstalovaných aplikací, rolí a funkcí (inventář softwaru). Doba trvání závisí na počtu zjištěných serverů. Pro servery 500 trvá trvat přibližně jednu hodinu, než se zjištěný inventář zobrazí na portálu Azure Migrate.

## <a name="review-and-export-the-inventory"></a>Kontrola a export inventáře

Po dokončení inventáře softwaru můžete zkontrolovat a vyexportovat inventář v Azure Portal.

1. V **Azure Migrate-servery**  >  **Azure Migrate: vyhodnocování serveru** klikněte na zobrazený počet a otevřete stránku **zjištěné servery** .

    > [!NOTE]
    > V této fázi můžete volitelně taky povolit analýzu závislostí pro zjištěné servery, abyste mohli vizualizovat závislosti mezi servery, které chcete vyhodnotit. [Přečtěte si další informace](concepts-dependency-visualization.md) o analýze závislostí.

2. Ve sloupci **zjištěné aplikace** kliknutím na zobrazený počet zkontrolujte zjištěné aplikace, role a funkce.
4. Pokud chcete exportovat inventář, klikněte na stránce **zjištěné servery** na **exportovat inventář aplikací**.

Inventář aplikací je exportován a stažen ve formátu aplikace Excel. V listu **inventáře aplikací** se zobrazí všechny aplikace zjištěné napříč všemi servery.

## <a name="discover-sql-server-instances-and-databases"></a>Zjištění instancí a databází SQL Server

- Zjišťování aplikací také identifikuje instance SQL Server spuštěných ve vašem prostředí VMware.
- Pokud jste v nástroji Configuration Manager pro zařízení nezadali přihlašovací údaje pro ověřování systému Windows nebo SQL Server pověření, přidejte přihlašovací údaje, aby je zařízení mohl používat k připojení k příslušným SQL Server instancí.

Po připojení zařízení shromáždí údaje o konfiguraci a výkonu SQL Server instance a databáze. Konfigurační data SQL Server se aktualizují jednou za 24 hodin a data o výkonu se zaznamenávají každých 30 sekund. Proto jakékoli změny vlastností instance SQL Server a databází, jako je stav databáze, úroveň kompatibility atd. může trvat až 24 hodin, než se aktualizuje na portálu.

## <a name="next-steps"></a>Další kroky

- [Vytvořte posouzení](how-to-create-assessment.md) pro zjištěné servery.
- [Posuzuje servery SQL](./tutorial-assess-sql.md) pro migraci do Azure SQL.
