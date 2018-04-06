---
title: Začínáme s Azure Automation
description: Tento článek obsahuje přehled služby Azure Automation. Ho zkontroluje podrobnosti o návrhu a implementace v rámci přípravy registrace nabídky z Azure Marketplace.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 2fcbbc2532e5cb9963922b4987ba0c7080fdb170
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="get-started-with-azure-automation"></a>Začínáme s Azure Automation

Tento článek představuje základní koncepty související s nasazením Azure Automation. Pokud začínáte Automation v Azure nebo se zkušenostmi s nástrojem automatizace pracovního postupu software System Center Orchestrator, můžete se dozvědět, jak připravit a zařadit automatizace. Po přečtení tohoto článku, budete připraveni začít vývoj runbooky na podporu vašich automatizačních potřeb procesu. 


## <a name="automation-architecture-overview"></a>Přehled architektury služby Automation

![Přehled Azure Automation](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Služby Azure Automation je software jako služba (SaaS) aplikace, která umožňuje škálovatelného a spolehlivého víceklientském prostředí ve kterém můžete použít k automatizaci procesů sady runbook. Konfigurace požadovaného stavu (DSC) můžete použít v Azure, jiných cloudových služeb, nebo v místním prostředí a spravovat konfiguraci změny systémy Windows a Linux. Entity v účtu Automation, včetně sady runbook, prostředky a účty spustit jako jsou izolované od jiných účtech Automation v rámci vašeho předplatného a od jiných předplatných.  

Sady Runbook, které běží v Azure jsou spouštěny na automatizace izolovaných prostorů. Izolovaných prostorů jsou hostované v platformy Azure jako virtuální počítače služba (PaaS). 

Automatizace izolovaných prostorů poskytují izolaci klientů pro všechny aspekty spuštění sady runbook, včetně modulů, úložiště, paměť, síťové komunikace a datové proudy úlohy. Tato role je spravované službou. Nelze získat přístup, nebo z účtu Azure nebo Automatizace správy rolí.         

K automatizaci nasazení a správu prostředků ve vašem místním datovém centru nebo jiných cloudových služeb, po vytvoření účtu Automation, můžete určit jeden nebo více virtuálních počítačů ke spuštění [hybridní pracovní proces Runbooku](automation-hybrid-runbook-worker.md) role. Každý hybridní pracovní proces Runbooku vyžaduje Microsoft Management agenta k instalaci a účet Automation. Agent musí mít připojení k pracovnímu prostoru analýzy protokolů Azure. Analýzy protokolů můžete použít k navázání připojení v instalaci, údržbě Microsoft Management agenta a monitorování funkčnosti hybridního pracovního procesu Runbooku. Automatizace Azure provádí doručování sady runbook a pokyny, abyste je mohli spustit.

Můžete nasadit více procesy Hybrid Runbook Worker. Pro zajištění vysoké dostupnosti pro sady runbook a vyrovnávání zatížení úlohy sady runbook pomocí procesů Hybrid Runbook Worker. V některých případech můžete vyhradit úlohy sady runbook pro konkrétní úlohy nebo prostředí. Microsoft Monitoring Agent na hybridní pracovní proces Runbooku inicializuje komunikaci s služby Automation přes TCP port 443. Procesy hybrid Runbook Worker nemá žádné brány firewall pro příchozí požadavky.  

Můžete chtít sadu runbook, která běží na hybridní pracovní proces Runbooku k provádění úloh správy na jiné počítače nebo služby ve vašem prostředí. V tomto scénáři sada runbook může být taky potřebujete přístup k další porty. Pokud vaše zásady zabezpečení IT Nepovolit počítače v síti pro připojení k Internetu, zkontrolujte [OMS brány](../log-analytics/log-analytics-oms-gateway.md). OMS brány funguje jako proxy pro hybridní pracovní proces Runbooku. Ho shromažďovat stav úlohy a přijímat informace o konfiguraci z vašeho účtu Automation.

Sady Runbook, které běží na hybridní pracovní proces Runbooku se spouštějí v kontextu účtu místního systému v počítači. Při provádění akcí na místním počítači Windows doporučujeme kontextu zabezpečení. Pokud chcete runbook ke spouštění úloh na prostředky, které jsou mimo místní počítač, musíte může definovat zabezpečené přihlašovací údaje prostředky v účtu Automation. Můžete používat prostředky zabezpečené přihlašovací údaje ze sady runbook a použít je k ověření pomocí externího zdroje. Můžete použít [pověření](automation-credentials.md), [certifikát](automation-certificates.md), a [připojení](automation-connections.md) prostředky ve vašem runbooku. Prostředky pomocí rutin, které můžete zadat pověření k jejich ověření.

Můžete použít konfigurace DSC, které jsou uložené ve službě Azure Automation k virtuálním počítačům. Další fyzické a virtuální počítače může požádat o konfiguraci ze serveru vyžádané replikace Automation DSC. Nemusíte nasazovat jakékoliv infrastruktury pro podporu serveru vyžádané replikace Automation DSC ke správě konfigurace vaší místní fyzické nebo virtuální systémy Windows a Linux. Potřebujete jenom odchozí přístup k Internetu každého systému, která bude spravovat pomocí Automation DSC. Komunikace probíhá přes port 443 protokolu TCP ke službě Analýza protokolů.   

## <a name="prerequisites"></a>Požadavky

### <a name="automation-dsc"></a>Automatizace DSC
Automatizace DSC můžete použít ke správě těchto počítačů:

* Virtuální počítače Azure (klasický) s Windows nebo Linux.
* Virtuální počítače Azure s Windows nebo Linux.
* Amazon Web Services (AWS) virtuálních počítačů s Windows nebo Linux.
* Fyzické a virtuální počítače Windows, které jsou místní nebo v cloudu, než Azure nebo AWS.
* Fyzické a virtuální počítače Linux, které jsou místní nebo v cloudu, než Azure nebo AWS.

Pro počítače s Windows musí být nainstalována nejnovější verze služby Windows Management Framework (WMF) 5. Pro počítače se systémem Linux, nejnovější verzi [DSC Powershellu agenta pro Linux](https://www.microsoft.com/en-us/download/details.aspx?id=49150) musí být nainstalován. DSC prostředí PowerShell agent používá ke komunikaci s automatizace WMF 5. 

### <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker  
Pokud určíte počítač ke spuštění úlohy sady runbook hybridní, počítač musí splňovat následující požadavky:

* Windows Server 2012 nebo novější.
* Windows PowerShell 4.0 nebo novější Pro větší spolehlivost doporučujeme prostředí Windows PowerShell 5.0. Můžete [stáhnout nové verze](https://www.microsoft.com/download/details.aspx?id=50395) z webu Microsoft Download Center.
* Rozhraní .NET framework 4.6.2 nebo novější.
* Minimálně dvě jádra.
* Minimálně 4 GB paměti RAM.

### <a name="permissions-required-to-create-an-automation-account"></a>Oprávnění potřebná k vytvoření účtu Automation
Vytvořit nebo aktualizovat účet automatizace a splnit úkoly popsané v tomto článku, musíte mít následující oprávnění a oprávnění:   
 
* Pokud chcete vytvořit účet Automation, musí být účtu uživatele Azure Active Directory (Azure AD) přidaný k roli s oprávnění na roli vlastníka pro **Microsoft.Automation** prostředky. Další informace najdete v tématu [řízení přístupu na základě rolí ve službě Azure Automation](automation-role-based-access-control.md).  
* Na portálu Azure v části **Azure Active Directory** > **SPRAVOVAT** > **registrace aplikace**, pokud **registrace aplikace**  je nastaven na **Ano**, mohou uživatelé bez oprávnění správce v klientovi služby Azure AD [registraci aplikace služby Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions). Pokud **registrace aplikace** je nastaven na **ne**, uživatele, který provádí tato akce musí být globálním správcem ve službě Azure AD. 

Pokud nejste členem odběru instanci Active Directory předtím, než jsou přidány do role Globální správce nebo spolusprávce odběru, jsou přidány do služby Active Directory jako Host. V tomto scénáři se zobrazí tato zpráva na **přidat účet Automation** stránky: "Nemáte oprávnění k vytvoření." 

Pokud je uživatel přidaný k roli globálního správce nebo spolusprávce nejprve můžete můžete je odebrat z instance odběru služby Active Directory a potom je znovu přidat na úplné role uživatele ve službě Active Directory.

Ověření role uživatele:
1. V portálu Azure přejděte do **Azure Active Directory** podokně.
2. Vyberte **uživatelů a skupin**.
3. Vyberte **všichni uživatelé**. 
4. Po výběru konkrétního uživatele, vyberte **profil**. Hodnota **typ uživatele** atribut v profilu uživatele by neměl být **hosta**.

## <a name="authentication-planning"></a>Plán pro ověřování
Ve službě Azure Automation můžete automatizovat úlohy s prostředky, které jsou v Azure, místně a v jiných cloudových služeb. Pro sadu runbook provádět požadované akce musí mít oprávnění pro bezpečný přístup k prostředkům. Musí mít minimální práva potřebná v rámci předplatného.  

### <a name="what-is-an-automation-account"></a>Co je účet služby Automation 
Všechny úlohy automatizace, které s prostředky provádíte pomocí rutin ve službě Azure Automation ověřování v Azure pomocí ověřování na základě přihlašovacích údajů organizační identity Azure AD.  Účet automatizace je nezávislý na účet, který používáte pro přihlášení k portálu pro konfiguraci a používání prostředků Azure. 

V následujících zdrojích informací jsou součástí účet Automation:

* **Certifikáty**. Obsahuje certifikát, který se používá k ověřování ze sady runbook nebo konfigurace DSC. Můžete také přidat certifikáty.
* **Připojení**. Obsahuje informace o ověřování a konfigurace, které je nutné se připojit externí služby nebo aplikace ze sady runbook nebo konfigurace DSC.
* **Přihlašovací údaje**. Obsahuje **PSCredential** objekt, který má pověření zabezpečení, jako je například uživatelské jméno a heslo. Přihlašovací údaje jsou potřebné k ověření ze sady runbook nebo konfigurace DSC.
* **Integrační moduly**. Moduly Powershellu, které jsou součástí účtu Automation. Moduly prostředí PowerShell pomocí rutin v runboocích a konfiguracích DSC.
* **Plány**. Obsahuje plány, které spuštění nebo zastavení sady runbook v zadanou dobu, včetně opakovaných frekvencí.
* **Proměnné**. Obsahují hodnoty, které jsou k dispozici ze sady runbook nebo konfigurace DSC.
* **Konfigurace DSC**. Skripty prostředí PowerShell, které popisují, jak nakonfigurovat nastavení nebo funkce operačního systému, nebo jak nainstalovat aplikace na počítači s Windows nebo Linux.  
* **Sady Runbook**. Sadu úloh, které provádějí automatizovaného procesu v automatizace založené na prostředí Windows PowerShell.    

Prostředky Automation jednotlivých účtů Automation jsou přidružené k jedné oblasti Azure. Účty Automation můžete však použít ke správě všechny prostředky ve vašem předplatném. Účty Automation vytvořte v různých oblastech, pokud máte zásady, které vyžadují izolaci dat a prostředků v určité oblasti.

Když vytvoříte účet Automation na portálu Azure, se automaticky vytvoří dvě entity ověřování:

* **Účet Spustit jako**. Tento účet provede následující úlohy:
  - Vytvoří objekt služby ve službě Azure AD.
  - Vytvoří certifikát.
  - Přiřadí Contributor Role-Based řízení přístupu (RBAC), která spravuje prostředky Azure Resource Manageru pomocí sad runbook.
* **Classic účet Spustit jako**. Tento účet odešle certifikát pro správu. Certifikát se používá ke správě klasické prostředky pomocí sad runbook.

RBAC je k dispozici s Resource Managerem k udělování povolených akcí na účtu uživatele Azure AD a účet Spustit jako. RBAC můžete také použít k ověření tohoto objektu služby. Další informace a nápovědu k vývoji modelu pro správu oprávnění automatizace, najdete v části [řízení přístupu na základě rolí v Azure Automation článku](automation-role-based-access-control.md).  

#### <a name="authentication-methods"></a>Metody ověřování
Následující tabulka shrnuje metod ověřování, které můžete použít pro každé prostředí, který podporuje Azure Automation.

| Metoda | Prostředí 
| --- | --- | 
| Účet Spustit jako pro Azure a Spustit jako pro Azure Classic |Azure Resource Manager a nasazení Azure Classic |  
| Uživatelský účet Azure AD |Azure Resource Manager a nasazení Azure Classic |  
| Ověřování systému Windows |Místní datacenter nebo ostatní poskytovatele cloudových služeb pomocí role hybridní pracovní proces Runbooku |  
| Přihlašovací údaje služby Amazon Web Services |Amazon Web Services |  

Následující články poskytují přehled a doporučený postup konfigurace ověřování pro tato prostředí. Články popisují používání existující a nový účet, který můžete vyhradit pro prostředí. 
* [Vytvořit samostatný účet Azure Automation.](automation-create-standalone-account.md)
* [Ověření Runbooků pomocí Azure nasazení classic a Resource Manager](automation-create-aduser-account.md)
* [Ověření Runbooků pomocí Amazon Web Services](automation-config-aws-account.md)
* [Aktualizace účtu Automation spustit jako](automation-create-runas-account.md)

Pro účty spustit v Azure jako a Classic spustit jako [aktualizace účtu Automation spustit jako](automation-create-runas-account.md) popisuje postup aktualizace existujícího účtu automatizace s účty spustit jako z portálu. Také popisuje, jak pomocí prostředí PowerShell, pokud účet Automation nebyl původně nakonfigurované s účtem spustit jako nebo Classic spustit jako. Účet Spustit jako a účet Classic spustit jako můžete vytvořit pomocí certifikátu, který vystavila vaše podnikové certifikační autority (CA). Zkontrolujte [aktualizace účtu Automation spustit jako](automation-create-runas-account.md) se dozvíte, jak vytvořit účty pomocí této konfigurace.     
 
## <a name="network-planning"></a>Plánování sítě
Pro Runbook Worker hybridní připojení k a zaregistrovat u analýzy protokolů musí mít přístup k adresám URL, které jsou popsané v této části a číslo portu. Toto je kromě [portů a adres URL potřebných pro agenta Microsoft Monitoring Agent](../log-analytics/log-analytics-windows-agent.md) pro připojení k analýze protokolů. 

Pokud používáte proxy server pro komunikaci mezi agentem a analýzy protokolů služby, zajistěte, aby byly dostupné s příslušnými prostředky. Pokud používáte bránu firewall k omezení přístupu k Internetu, musíte nakonfigurovat bránu firewall tak, aby povolovala přístup.

Následující portů a adres URL jsou vyžadovány pro roli hybridní pracovní proces Runbooku ke komunikaci s automatizace:

* Port: Pouze TCP 443 je vyžadována pro odchozí přístup k Internetu.
* Global URL: *.azure-automation.net.

Pokud máte účet Automation, který je definován pro určitou oblast, můžete omezit komunikaci s místní stejné datové centrum. Následující tabulka obsahuje záznam DNS pro každou oblast.

| **Oblast** | **Záznam DNS** |
| --- | --- |
| Střed USA – jih |scus-jobruntimedata-prod-su1.azure-automation.net |
| Východní USA 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| Západní střed USA | wcus-jobruntimedata-prod-su1.azure-automation.net |
| Západní Evropa |we-jobruntimedata-prod-su1.azure-automation.net |
| Severní Evropa |ne-jobruntimedata-prod-su1.azure-automation.net |
| Střední Kanada |cc-jobruntimedata-prod-su1.azure-automation.net |
| Jihovýchodní Asie |sea-jobruntimedata-prod-su1.azure-automation.net |
| Střed Indie |cid-jobruntimedata-prod-su1.azure-automation.net |
| Japonsko – východ |jpe-jobruntimedata-prod-su1.azure-automation.net |
| Austrálie – jihovýchod |ase-jobruntimedata-prod-su1.azure-automation.net |
| Spojené království – jih | uks-jobruntimedata-prod-su1.azure-automation.net |
| USA (Gov) – Virginia | usge-jobruntimedata-prod-su1.azure-automation.us |

Seznam IP adres oblasti namísto názvů oblast, stáhněte si [Azure Datacenter IP adresu](https://www.microsoft.com/download/details.aspx?id=41653) souboru XML z webu Microsoft Download Center. 

> [!NOTE]
> Soubor XML adresu IP Datacentra Azure seznam rozsahů IP adres, které se používají v datových centrech Microsoft Azure. Výpočty, SQL a úložiště rozsahy jsou zahrnuty v souboru. 
>
>Aktualizovaný soubor odeslání každý týden. Soubor odráží aktuálně nasazená rozsahy a žádné nadcházející změny rozsahy IP. Nové rozsahy, které se zobrazují v souboru nebudou používány v datacentru alespoň jeden týden. 
>
> Je vhodné stáhnout nový soubor XML každý týden. Aktualizujte lokalitu se správně identifikují služby spuštěné v Azure. Azure ExpressRoute uživatelé Upozorňujeme, že tento soubor se používá k aktualizaci inzerování protokolu BGP (Border Gateway) Azure místa první týden v měsíci. 
> 

## <a name="creating-an-automation-account"></a>Vytvoření účtu Automation

Následující tabulka uvádí metody pro vytvoření účtu Automation na portálu Azure. Tabulka popisuje každý typ nasazení a rozdíly mezi nimi.  

|Metoda | Popis |
|-------|-------------|
| Vyberte **automatizace a řízení** v Azure Marketplace | Nabídky Azure Marketplace vytvoří účet Automation a pracovní prostor analýzy protokolů, které jsou propojené a ve stejné skupině prostředků a oblast. Integrace s analýzy protokolů také zahrnuje výhodou používání monitorovat a analyzovat runbook úlohy stav úlohy datové proudy a v čase. Také můžete pokročilých funkcí v analýzy protokolů eskalovat nebo prozkoumat problémy. Nabídku nasadí **sledování změn** a **správy aktualizací** řešení, které jsou ve výchozím nastavení povolené. |
| Vyberte **automatizace** na webu Marketplace | Tato metoda vytváří účet Automation ve skupině nový nebo existující prostředek, který není přidružený k pracovnímu prostoru analýzy protokolů. Neobsahuje žádné dostupné řešení z **automatizace a řízení** nabídky. Tato metoda je základní konfigurace, které vás seznámí se automatizace. Další informace o zápisu runboocích a konfiguracích DSC, a zjistěte, jak používat funkce služby může pomoct. |
| Vyberte **správy** řešení | Pokud vyberete **správy** řešení, včetně [správy aktualizací](../operations-management-suite/oms-solution-update-management.md), [spuštění a zastavení virtuálních počítačů během mimo špičku](automation-solution-vm-management.md), nebo [sledování změn](../log-analytics/log-analytics-change-tracking.md), řešení zobrazí výzva k výběru existující účet Automation a pracovní prostor analýzy protokolů. Řešení nabízí možnost vytvořit účet Automation a pracovní prostor analýzy protokolů podle potřeby pro řešení nasadit v rámci vašeho předplatného. |

### <a name="create-an-automation-account-thats-integrated-with-log-analytics"></a>Vytvoření účtu Automation, která je integrovaná s analýzy protokolů
Chcete zařadit automatizace, doporučujeme, že jste vybrali **automatizace a řízení** nabídky na Marketplace. Pomocí této metody se vytvoří účet Automation a vytvoří integrace s pracovní prostor analýzy protokolů. Pokud použijete tuto metodu, máte také možnost instalace řešení pro správu, které jsou k dispozici v nabídce.  

[Vytvořit samostatný účet Automation](automation-create-standalone-account.md) vás provede procesem vytvoření účet Automation a pracovní prostor analýzy protokolů pomocí registrace **automatizace a řízení** nabídky. Můžete zjistěte, jak vytvořit samostatný účet Automation pro testování nebo náhled službu.  

Vytvoření účtu Automation a pracovní prostor analýzy protokolů pomocí **automatizace a řízení** nabídky Marketplace:

1. Přihlaste se k portálu Azure pomocí účtu, který je členem role Správci předplatného a spolusprávce předplatného.
2. Vyberte **nové**.<br><br> ![Na portálu Azure vyberte nový.](media/automation-offering-get-started/automation-portal-martketplacestart.png)<br>  
3. Vyhledejte **automatizace**. Ve výsledcích hledání vyberte **automatizace a řízení**.<br><br> ![Vyhledejte a vyberte ovládací prvek a Automation v Azure Marketplace](media/automation-offering-get-started/automation-portal-martketplace-select-automationandcontrol.png).<br>   
4. Přečtěte si popis nabídky a pak vyberte **vytvořit**.  
5. V části **automatizace a řízení**, vyberte **pracovním prostorem OMS**. V části **pracovních prostorů OMS**, vyberte pracovní prostor analýzy protokolů, propojené předplatné Azure, účet Automation je v. Pokud nemáte pracovní prostor analýzy protokolů, vyberte **vytvořit nový pracovní prostor**. V části **pracovním prostorem OMS**: 
  1. Pro **pracovním prostorem OMS**, zadejte název pro nový pracovní prostor.
  2. Pro **předplatné**, vybrat odběr, který má být propojen s. Není-li výchozí výběr předplatné, které chcete použít, vyberte z rozevíracího seznamu předplatné.
  3. Pro **skupiny prostředků**, můžete vytvořit skupinu prostředků nebo vyberte existující skupinu prostředků.  
  4. Pro **umístění**, vyberte oblast. Další informace najdete v tématu [oblasti, které jsou k dispozici v Azure Automation](https://azure.microsoft.com/regions/services/). Řešení jsou nabízena v dvou vrstev: úroveň free a na uzel (OMS). Úroveň free může mít na množství dat, která shromáždil denně, dobu uchovávání informací a minut runtime úlohy sady runbook. Jednom uzlu (OMS) vrstvy nemá omezení na množství dat denně shromážděných.  
  5. Vyberte **Účet Automation**.  Pokud vytvoříte nový pracovní prostor analýzy protokolů, musíte také vytvořit účet Automation, který je spojen s nový pracovní prostor analýzy protokolů. Zahrnují vaše předplatné, skupinu prostředků a oblast. 
    1. Vyberte **vytvořit účet Automation**.
    2. V části **účet Automation**v **název** pole, zadejte název účtu služby Automation.
    Všechny ostatní možnosti se vyplní automaticky na základě pracovního prostoru analýzy protokolů vybrané. Tyto možnosti se nedá změnit. 
    3. Účet Spustit v Azure jako představuje výchozí metodu ověřování pro tuto nabídku. Po výběru **OK**, se ověřují možností konfigurace a vytvoření účtu Automation. Chcete-li sledovat její průběh v nabídce vyberte **oznámení**. 
    4. Další možností je vybrat existující účet služby Automation Spustit jako. Účet, který jste vybrali nelze již propojit s jinou pracovní prostor analýzy protokolů. Pokud se jedná, zobrazí se zpráva oznámení. Pokud účet je již propojený s pracovní prostor analýzy protokolů, vyberte jiný účet Automation spustit jako nebo vytvořit.
    5. Po zadejte nebo vyberte požadované informace, vyberte **vytvořit**. Informace o ověření a vytvoření účtů účet Automation a spustit jako. Automaticky se vrátíte na **pracovním prostorem OMS** podokně.  
6. Po zadejte nebo vyberte požadované informace na **pracovním prostorem OMS** podokně, vyberte **vytvořit**.  Ověření informací a je vytvořen v pracovním prostoru. Chcete-li sledovat její průběh v nabídce vyberte **oznámení**. Vrátíte se **přidat řešení** podokně.  
7. V části **automatizace a řízení** nastavení, potvrďte, že chcete instalovat doporučené řešení zkontrolujte předem vybrané. Pokud změníte některá výchozí možnosti, můžete nainstalovat řešení jednotlivě později.  
8. Chcete-li pokračovat s registrací ve službě Automation a pracovní prostor analýzy protokolů, vyberte **vytvořit**. Všechna nastavení ověření a poté se pokusí nasazení nabídky v rámci vašeho předplatného Azure. Tento proces může trvat několik sekund. Chcete-li sledovat průběh, v nabídce vyberte **oznámení**. 

Jakmile nabídce zařazený nemá, můžete provést následující úlohy:
* Zahájení vytváření sad runbook.
* Spolupracovat s řešení pro správu, které jste povolili.
* Nasazení [hybridní pracovní proces Runbooku](automation-hybrid-runbook-worker.md) role.
* Zahájení práce s [analýzy protokolů](https://docs.microsoft.com/azure/log-analytics) ke shromažďování dat, která je generován prostředků ve vašem prostředí cloudu nebo místně.   

## <a name="next-steps"></a>Další postup
* Ověřte, že váš nový účet Automation se můžou ověřovat proti prostředků Azure. Další informace najdete v tématu [Test spustit v Azure Automation jako ověření účtu](automation-verify-runas-authentication.md).
* Zjistěte, jak začít s vytvářením sad runbook a souvisejících hledisek, než začnete, vytváření obsahu. Další informace najdete v tématu [typy runbooků Automation](automation-runbook-types.md).


