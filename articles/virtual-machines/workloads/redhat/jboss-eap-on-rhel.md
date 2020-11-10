---
title: Rychlý Start – nasazení JBoss podnikové aplikační platformy (EAP) na Red Hat Enterprise Linux (RHEL) na virtuální počítače Azure a služby Virtual Machine Scale Sets
description: Jak nasadit podnikové aplikace v jazyce Java pomocí Red Hat JBoss EAP na virtuálních počítačích Azure RHEL a Virtual Machine Scale Sets.
author: theresa-nguyen
ms.author: bicnguy
ms.topic: quickstart
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.assetid: 8a4df7bf-be49-4198-800e-db381cda98f5
ms.date: 10/30/2020
ms.openlocfilehash: ce07a0667b1fd4b439f061966e4ee0b1112578c4
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94413203"
---
# <a name="deploy-enterprise-java-applications-to-azure-with-jboss-eap-on-red-hat-enterprise-linux"></a>Nasazení podnikových aplikací v jazyce Java do Azure pomocí protokolu JBoss EAP na Red Hat Enterprise Linux

Šablony pro rychlý Start Azure v tomto článku vám ukážou, jak nasadit [JBoss Enterprise Application Platform (EAP)](https://www.redhat.com/en/technologies/jboss-middleware/application-platform) pomocí služby [Red Hat Enterprise Linux (RHEL)](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux) na virtuální počítače Azure a sady škálování virtuálních počítačů. K ověření nasazení použijete ukázkovou aplikaci Java. 

JBoss EAP je open source platforma aplikačního serveru. Zajišťuje zabezpečení, škálovatelnost a výkon na podnikové úrovni pro aplikace v jazyce Java. RHEL je open source platforma operačního systému (OS). Umožňuje škálování stávajících aplikací a zavádění vznikajících technologií napříč všemi prostředími. 

JBoss EAP a RHEL obsahují všechno, co potřebujete k sestavování, spouštění, nasazování a správě podnikových aplikací v jazyce Java v jakémkoli prostředí. Kombinace je open source řešení pro místní, virtuální prostředí a v privátních, veřejných i hybridních cloudech.

## <a name="prerequisites"></a>Předpoklady 

* Účet Azure s aktivním předplatným. Pokud chcete získat předplatné Azure, aktivujte si své [kredity Azure pro předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) nebo si [vytvořte účet zdarma](https://azure.microsoft.com/pricing/free-trial).

* JBoss instalaci protokolu EAP. Musíte mít účet Red Hat s nárokem na RHSM (Red Hat Subscription Management) pro JBoss EAP. Tato oprávnění vám umožní stáhnout verzi protokolu EAP s Red Hat testovánou a certifikovaným JBoss.  

  Pokud nemáte nárok na protokol EAP, Získejte před zahájením [zkušební předplatné JBoss EAP](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) . Pokud chcete vytvořit nové předplatné Red Hat, navštivte [zákaznický portál Red Hat](https://access.redhat.com/) a nastavte účet.

* Rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/overview)

* RHEL možnosti. Vyberte si průběžné platby (PAYG) nebo Přineste si vlastní předplatné (BYOS). Pomocí BYOS je třeba před nasazením šablony pro rychlé zprovoznění aktivovat image [Red Hat Cloud Access](https://access.redhat.com/) RHEL Gold.

## <a name="java-ee-and-jakarta-ee-application-migration"></a>Migrace aplikací z Java EE a Jakarta EE

### <a name="migrate-to-jboss-eap"></a>Migrace na JBoss EAP
JBoss EAP 7,2 a 7,3 jsou certifikované implementace Java Enterprise Edition (Java EE) 8 a Jakarta EE 8 specifikací. JBoss EAP poskytuje předkonfigurované možnosti pro funkce, jako je Clustering s vysokou dostupností (HA), zasílání zpráv a distribuované ukládání do mezipaměti. Umožňuje také uživatelům psát, nasazovat a spouštět aplikace pomocí různých rozhraní API a služeb, které poskytuje JBoss EAP.  

Další informace o protokolu EAP JBoss najdete v článku [Úvod do JBoss eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html-single/introduction_to_jboss_eap/index) nebo [Úvod do JBoss EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/introduction_to_jboss_eap/index).

 #### <a name="applications-on-jboss-eap"></a>Aplikace na JBoss protokolu EAP

* **Aplikace webových služeb**. Webové služby poskytují standardní způsob, jak vzájemně pracovat mezi softwarovými aplikacemi. Každá aplikace může běžet na různých platformách a architekturách. Tyto webové služby usnadňují interní a heterogenní komunikaci subsystému. 

  Další informace najdete v tématu [vývoj aplikací webové služby na základě protokolu eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_web_services_applications/index) nebo [vývoj aplikací webové služby na EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_web_services_applications/index).

* **Aplikace EJB (Enterprise Java fazole)**. EJB 3,2 je rozhraní API pro vývoj distribuovaných, transakčních, zabezpečených a přenosných aplikací pro Java EE a Jakarta et. EJB používá serverové komponenty s názvem Enterprise fazole k implementaci obchodní logiky aplikace v odděleném způsobu, který podporuje opakované použití. 

  Další informace najdete v tématu [vývoj aplikací EJB na eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_ejb_applications/index) nebo [vývoj aplikací EJB na EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_ejb_applications/index).

* **Aplikace v režimu hibernace**. Vývojáři a správci můžou vyvíjet a nasazovat rozhraní API pro trvalá rozhraní Java (JPA) a aplikace v režimu hibernace pomocí JBoss EAP. Jádro režimu hibernace je objektově-relační mapové rozhraní pro jazyk Java. Poskytuje rozhraní pro mapování objektově orientovaného modelu domény na relační databázi, takže aplikace se mohou vyhnout přímé interakci s databází. 

  Správce entit v režimu hibernace implementuje rozhraní pro programování a pravidla pro životní cyklus definovaná [specifikací JPA 2,1](https://www.jcp.org/en/jsr/overview). Spolu s poznámkami k režimu hibernace tato obálka implementuje kompletní řešení JPA (a samostatné), které se nachází na vyspělém jádru hibernace. 
  
  Další informace o režimu hibernace najdete v tématu [JPA on eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/development_guide/java_persistence_api) nebo  [Jakarta Persistence on EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/development_guide/java_persistence_api).

#### <a name="red-hat-migration-toolkit-for-applications"></a>Sada nástrojů pro migraci Red Hat pro aplikace
[Sada Red Hat Migration Toolkit for Applications (MTA)](https://developers.redhat.com/products/mta/overview) je migrační nástroj pro aplikační servery Java. Tento nástroj slouží k migraci z jiného aplikačního serveru na JBoss EAP. Funguje s moduly plug-in IDE pro [zatmění, IDE](https://www.eclipse.org/ide/), [Red Hat CodeReady](https://developers.redhat.com/products/codeready-workspaces/overview)a [Visual Studio Code](https://code.visualstudio.com/docs/languages/java) pro Java. 

MTA je bezplatný a open source nástroj, který:
* Automatizuje analýzu aplikací.
* Podporuje odhad úsilí.
* Zrychluje migraci kódu.
* Podporuje kontejnery.
* Integruje se s tvůrcem úloh Azure.

### <a name="migrate-jboss-eap-from-on-premises-to-azure"></a>Migrace protokolu EAP JBoss z místního prostředí do Azure
Nabídka Azure Marketplace JBoss EAP v RHEL se nainstaluje a zřídí na virtuálních počítačích Azure za méně než 20 minut. K těmto nabídkám máte přístup z [Azure Marketplace](https://azuremarketplace.microsoft.com/).

Tato Azure Marketplace nabídka obsahuje různé kombinace protokolů EAP a RHEL, které podporují vaše požadavky. JBoss EAP je vždycky BYOS, ale pro RHEL OS si můžete vybrat mezi BYOS nebo PAYG. Nabídka Azure Marketplace zahrnuje možnosti plánu pro JBoss EAP na RHEL jako samostatné nebo clusterované virtuální počítače:

* JBoss EAP 7,2 na virtuálním počítači s RHEL 7,7 (PAYG)
* JBoss EAP 7,2 na virtuálním počítači s RHEL 8,0 (PAYG)
* JBoss EAP 7,3 na virtuálním počítači s RHEL 8,0 (PAYG)
* JBoss EAP 7,2 na virtuálním počítači s RHEL 7,7 (BYOS)
* JBoss EAP 7,2 na virtuálním počítači s RHEL 8,0 (BYOS)
* JBoss EAP 7,3 na virtuálním počítači s RHEL 8,0 (BYOS)

Spolu s Azure Marketplace nabídkami můžete začít používat šablony pro rychlý Start na cestě k migraci do Azure. Tyto rychlé starty obsahují předem připravené šablony Azure Resource Manager (ARM) a skripty pro nasazení JBoss EAP v RHEL v různých konfiguracích a kombinacích verzí. Budete mít následující:

* Nástroj pro vyrovnávání zatížení.
* Soukromá IP adresa pro vyrovnávání zatížení a virtuální počítače.
* Virtuální síť s jedinou podsítí.
* Konfigurace virtuálních počítačů (cluster nebo samostatná).
* Ukázková aplikace Java.

Architektura řešení pro tyto šablony zahrnuje:

* JBoss EAP na samostatném virtuálním počítači s RHEL.
* JBoss EAP cluster napříč několika virtuálními počítači RHEL.
* JBoss EAP prostřednictvím služby Azure Virtual Machine Scale Sets.

#### <a name="linux-workload-migration-for-jboss-eap"></a>Migrace úloh Linux pro JBoss EAP
Tvůrce úloh Azure zjednodušuje proces testování, vyhodnocení a migrace místních aplikací Java do Azure. Tvůrce úloh se integruje s nástrojem pro zjišťování Azure Migrate a identifikuje servery EAP s JBoss. Pak dynamicky vygeneruje Ansible PlayBook pro nasazení serveru JBoss EAP. Pomocí nástroje Red Hat MTA migruje servery z jiných aplikačních serverů do JBoss EAP. 

Mezi kroky pro zjednodušení migrace patří:
1. **Vyhodnocení**. Vyhodnoťte clustery s JBoss EAP pomocí virtuálního počítače Azure nebo sady škálování virtuálních počítačů.
1. **Posouzení**. Naskenujte aplikace a infrastrukturu.
1. **Konfigurace infrastruktury** Vytvořte profil úlohy.
1. **Nasazení a testování**. Nasaďte, migrujte a otestujte zatížení.
1. **Konfigurace po nasazení**. Integraci s daty, monitorováním, zabezpečením, zálohováním a dalšími službami.

## <a name="server-configuration-choice"></a>Volba konfigurace serveru

Pro nasazení virtuálního počítače s RHEL můžete zvolit buď PAYG, nebo BYOS. Obrázky z [Azure Marketplace](https://azuremarketplace.microsoft.com) PAYG jako výchozí. Pokud máte vlastní image operačního systému RHEL, nasaďte virtuální počítač s BYOS typem RHEL. Než nasadíte virtuální počítač nebo sadu škálování virtuálního počítače, ujistěte se, že váš účet RHSM má oprávnění BYOS přes cloudový přístup.

JBoss EAP obsahuje výkonné možnosti správy společně s poskytováním funkcí a rozhraní API aplikacím. Tyto možnosti správy se liší v závislosti na tom, jaký operační režim použijete ke spuštění protokolu JBoss EAP. Podporuje se na RHEL a ve Windows serveru. JBoss EAP nabízí samostatný serverový operační režim pro správu diskrétních instancí. Nabízí také operační režim spravované domény pro správu skupin instancí z jednoho řídicího bodu. 

> [!NOTE]
> JBoss EAP – spravované domény se v Microsoft Azure nepodporují, protože služby infrastruktury Azure spravují funkci HA. 

Proměnná prostředí `EAP_HOME` označuje cestu k instalaci protokolu JBoss EAP. Pomocí následujícího příkazu spusťte službu JBoss EAP v samostatném režimu:

```
$EAP_HOME/bin/standalone.sh
```
    
Tento spouštěcí skript používá soubor EAP_HOME/bin/Standalone.conf k nastavení některých výchozích předvoleb, jako jsou například možnosti JVM. V tomto souboru můžete upravit nastavení. JBoss EAP používá ke spuštění v samostatném režimu konfigurační soubor standalone.xml, který je ve výchozím nastavení spouštěný v samostatném režimu, ale můžete ho spustit pomocí jiného režimu. 

Podrobnosti o dostupných samostatných konfiguračních souborech a jejich použití najdete v tématu [samostatné konfigurační soubory serveru pro eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files) nebo [samostatné soubory konfigurace serveru pro EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files). 

K zahájení JBoss protokolu EAP s jinou konfigurací použijte `--server-config` argument. Zde je příklad:
    
 ```
 $EAP_HOME/bin/standalone.sh --server-config=standalone-full.xml
 ```
    
Úplný seznam všech dostupných argumentů spouštěcího skriptu a jejich účelů získáte pomocí `--help` argumentu. Další informace najdete v tématu [argumenty pro modul runtime serveru v protokolech eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime) nebo [v argumentech za běhu serveru na EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime).
    
JBoss EAP může fungovat i v režimu clusteru. Zasílání zpráv clusteru JBoss EAP umožňuje seskupení přenosových serverů JBoss EAP ke sdílení zátěže zpracování zpráv. Každý aktivní uzel v clusteru je aktivní JBoss Server pro zasílání zpráv s protokolem EAP, který spravuje vlastní zprávy a zpracovává vlastní připojení. Další informace najdete v tématu Přehled [clusterů na základě protokolu eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuring_messaging/clusters_overview) nebo [ clusterů s přehledem protokolu EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuring_messaging/clusters_overview). 

## <a name="support-and-subscription-notes"></a>Poznámky k podpoře a předplatnému
Tyto šablony pro rychlý Start jsou nabízeny následujícím způsobem: 

- RHEL OS se nabízí jako PAYG nebo BYOS prostřednictvím modelu image Red Hat Gold.
- JBoss EAP se nabízí jenom jako BYOS.

#### <a name="using-rhel-os-with-the-payg-model"></a>Použití operačního systému RHEL s modelem PAYG

Ve výchozím nastavení tyto šablony pro rychlý Start používají pro Azure Marketplace image PAYG na vyžádání RHEL 7,7 nebo 8,0. PAYG image mají za normální náklady na výpočetní výkon, síť a úložiště další poplatky za RHEL předplatné. V současné době je instance zaregistrovaná v rámci vašeho předplatného Red Hat. To znamená, že budete používat jedno z vašich nároků. 

Tento PAYG obrázek bude mít za následek "dvojitou fakturaci". Tomuto problému se můžete vyhnout vytvořením vlastní image RHEL. Pokud se chcete dozvědět víc, přečtěte si článek znalostní báze Red Hat Knowledge Base, [jak zřídit virtuální počítač s RHEL pro Microsoft Azure](https://access.redhat.com/articles/uploading-rhel-image-to-azure). Nebo aktivujte obrázek [Red Hat Cloud Access](https://access.redhat.com/) RHEL Gold.

Podrobnosti o cenách virtuálních počítačů s PAYG najdete v článku o [cenách Red Hat Enterprise Linux](https://azure.microsoft.com/pricing/details/virtual-machines/red-hat/). Pokud chcete používat RHEL v modelu PAYG, budete potřebovat předplatné Azure se zadaným způsobem platby pro [RHEL 7,7 v Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux77-ARM) nebo [RHEL 8,0 na Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux80-ARM). Tyto nabídky vyžadují, aby se v předplatném Azure zadal způsob platby.

#### <a name="using-rhel-os-with-the-byos-model"></a>Použití operačního systému RHEL s modelem BYOS

Pokud chcete používat BYOS pro RHEL operační systém, musíte mít platné předplatné Red Hat s nároky na používání RHEL operačního systému v Azure. Před nasazením operačního systému RHEL s modelem BYOS proveďte následující předpoklady:

1. Ujistěte se, že máte oprávnění RHEL pro operační systém a JBoss EAP připojené k vašemu předplatnému Red Hat.
2. Ověřte ID předplatného Azure, abyste mohli používat image RHEL BYOS. Dokončete proces pomocí [dokumentace správy předplatných Red Hat](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/con-enable-subs) , která zahrnuje tyto kroky:

   1. Povolte Microsoft Azure jako poskytovatele na řídicím panelu Red Hat Cloud Access.

   1. Přidejte ID vašich předplatných Azure.

   1. Povolte nové produkty pro cloudový přístup k Microsoft Azure.
    
   1. Aktivujte image Red Hat Gold pro vaše předplatné Azure. Další informace najdete v tématu [Image Red Hat Gold na Microsoft Azure](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure).

   1. Počkejte, až budou image Red Hat Gold dostupné ve vašem předplatném Azure. Tyto image jsou obvykle k dispozici do tří hodin od odeslání.
    
3. Přijměte Azure Marketplace podmínky a ujednání pro Image BYOS pro RHEL. Tento proces můžete dokončit spuštěním následujících příkazů rozhraní příkazového řádku Azure. Další informace najdete v tématu [RHEL BYOS Gold images v](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/byos) dokumentaci k Azure. Je důležité, abyste spustili nejnovější verzi rozhraní příkazového řádku Azure CLI.

   1. Otevřete relaci Azure CLI a ověřte ji pomocí svého účtu Azure. Pomoc najdete v tématu [přihlášení pomocí Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli).

   1. Spuštěním následujícího příkazu rozhraní příkazového řádku ověřte, jestli jsou image RHEL BYOS ve vašem předplatném k dispozici. Pokud tady žádné výsledky nezískáte, ujistěte se, že je pro image RHEL BYOS aktivované předplatné Azure.
   
      ```
      az vm image list --offer rhel-byos --all
      ```

   1. Spusťte následující příkaz, který přijme podmínky Azure Marketplace pro RHEL 7,7 BYOS a RHEL 8,0 BYOS, v uvedeném pořadí:
      ```
      az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm77
      ``` 

      ```
      az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm8
      ``` 
   
Vaše předplatné je teď připravené nasazovat RHEL 7,7 nebo 8,0 BYOS na virtuálních počítačích Azure.

#### <a name="using-jboss-eap-with-the-byos-model"></a>Použití protokolu JBoss EAP s modelem BYOS

Protokol EAP JBoss je k dispozici v Azure pouze prostřednictvím modelu BYOS. Při nasazení této šablony je potřeba zadat přihlašovací údaje RHSM spolu s ID fondu RHSM s platnými nároky EAP. Pokud nemáte nárok na EAP, Získejte [zkušební předplatné JBoss EAP](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) před tím, než začnete.

## <a name="deployment-options"></a>Možnosti nasazení

Šablonu můžete nasadit následujícími způsoby:

- **PowerShell**. Nasaďte šablonu spuštěním následujících příkazů: 

  ```
  New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri <raw link to the template which can be obtained from github>
  ```
 
  Informace o instalaci a konfiguraci Azure PowerShell najdete v [dokumentaci k prostředí PowerShell](https://docs.microsoft.com/powershell/azure/).  

- Rozhraní příkazového **řádku Azure** Nasaďte šablonu spuštěním následujících příkazů:

  ```
  az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  az group deployment create --resource-group <my-resource-group> --template-uri <raw link to the template which can be obtained from github>
  ```

  Podrobnosti o instalaci a konfiguraci rozhraní příkazového řádku Azure najdete v tématu [instalace rozhraní](https://docs.microsoft.com/cli/azure/install-azure-cli)příkazového řádku.

- **Azure Portal.** Do Azure Portal můžete nasadit pomocí přechodu na šablony Azure pro rychlý Start, jak je uvedeno v následující části. Po práci v rychlém startu vyberte tlačítko **nasadit do Azure** nebo **Procházet na GitHubu** .

## <a name="azure-quickstart-templates"></a>Šablony Azure pro rychlé zprovoznění

Můžete začít pomocí jedné z následujících šablon pro rychlý Start pro JBoss EAP v RHEL, které vyhovují vašemu cíli nasazení:

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-standalone-rhel/"> JBoss EAP na RHEL (samostatný virtuální počítač)</a>. Tím dojde k nasazení webové aplikace s názvem JBoss-EAP v Azure na JBoss EAP 7,2 nebo 7,3, které běží na RHEL 7,7 nebo na virtuálním počítači s 8,0.

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-clustered-multivm-rhel/"> JBoss EAP na RHEL (clusterované, více virtuálních počítačů)</a>. Tím se nasadí webová aplikace s názvem EAP-Session-Replication na clusteru JBoss EAP 7,2 nebo 7,3, který běží na *n* počtu RHEL 7,7 nebo 8,0 virtuálních počítačů. Uživatel rozhodne hodnotu *n* . Všechny virtuální počítače se přidají do fondu back-end nástroje pro vyrovnávání zatížení.

* <a href="https://azure.microsoft.com/en-us/resources/templates/jboss-eap-clustered-vmss-rhel/"> JBoss EAP v RHEL (Clusterová, virtuální počítač s měřítkem)</a>. Tím se nasadí webová aplikace s názvem EAP-Session-Replication v clusteru JBoss EAP 7,2 nebo 7,3, který běží na RHEL 7,7 nebo 8,0 Virtual Machine Scale Sets.

## <a name="resource-links"></a>Odkazy na prostředky

* [Zvýhodněné hybridní využití Azure](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing)
* [Konfigurace aplikace Java pro Azure App Service](https://docs.microsoft.com/azure/app-service/configure-language-java)
* [JBoss EAP na Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [JBoss EAP na Azure App Service Linux](https://docs.microsoft.com/azure/app-service/quickstart-java)
* [Nasazení protokolu JBoss EAP na Azure App Service](https://github.com/JasonFreeberg/jboss-on-app-service)

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [JBoss EAP 7,2](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.2/).
* Přečtěte si další informace o [JBoss EAP 7,3](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.3/).
* Přečtěte si další informace o [správě předplatných Red Hat](https://access.redhat.com/products/red-hat-subscription-management).
* Přečtěte si o [úlohách Red Hat v Azure](https://aka.ms/rhel-docs).
* Nasaďte [JBoss EAP na virtuálním počítači s RHEL nebo v sadě škálování virtuálního počítače z Azure Marketplace](https://aka.ms/AMP-JBoss-EAP).
* Nasaďte [JBoss EAP na virtuálním počítači s RHEL nebo na sadu virtuálních počítačů s využitím šablon Azure pro rychlý Start](https://aka.ms/Quickstart-JBoss-EAP).
