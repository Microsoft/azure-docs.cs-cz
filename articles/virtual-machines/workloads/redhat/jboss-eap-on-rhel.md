---
title: Rychlé zprovoznění – JBoss Enterprise Application (EAP) na Red Hat Enterprise Linux (RHEL) do virtuálního počítače Azure a sady škálování virtuálních počítačů
description: Jak nasadit podnikové aplikace v jazyce Java pomocí Red Hat JBoss EAP na VIRTUÁLNÍm počítači Azure s RHEL a v sadě škálování virtuálního počítače.
author: theresa-nguyen
ms.author: bicnguy
ms.topic: quickstart
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.assetid: 8a4df7bf-be49-4198-800e-db381cda98f5
ms.date: 10/30/2020
ms.openlocfilehash: cfd465476aa8963de6093bccd5d4821ea2b29338
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395814"
---
# <a name="deploy-enterprise-java-applications-to-azure-with-jboss-eap-on-red-hat-enterprise-linux"></a>Nasazení podnikových aplikací v jazyce Java do Azure pomocí protokolu JBoss EAP na Red Hat Enterprise Linux

Tyto šablony pro rychlý Start vám ukáže, jak nasadit [JBoss EAP](https://www.redhat.com/en/technologies/jboss-middleware/application-platform) s [RHEL](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux) do Azure Virtual Machines (VM) a Virtual Machine Scale Sets. K ověření nasazení budete mít v nasazení ukázkovou aplikaci Java. JBoss EAP je open source platforma aplikačního serveru. Zajišťuje zabezpečení, škálovatelnost a výkon na podnikové úrovni pro aplikace v jazyce Java. RHEL je open source platforma operačního systému (OS). Umožňuje škálování stávajících aplikací a zavádění vznikajících technologií napříč všemi prostředími. JBoss EAP a RHEL zahrnují vše potřebné k sestavování, spouštění, nasazování a správě podnikových aplikací v jazyce Java v jakémkoli prostředí. Jedná se o skvělé open source řešení pro místní, virtuální prostředí a v privátních, veřejných i hybridních cloudech.

## <a name="prerequisite"></a>Požadavek 

* Účet Azure s aktivním předplatným. Pokud chcete získat předplatné Azure, aktivujte si své [kredity Azure pro předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) nebo si [vytvořte účet zdarma](https://azure.microsoft.com/pricing/free-trial).

* Instalace protokolu JBoss EAP – musíte mít účet Red Hat s nárokem RHSM (Red Hat Subscription Management) pro JBoss EAP. Tato oprávnění vám umožní stáhnout verzi protokolu EAP s Red Hat testovánou a certifikovaným JBoss.  Pokud nemáte nárok na protokol EAP, Získejte před zahájením [zkušební předplatné JBoss EAP](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) . Pokud chcete vytvořit nové předplatné Red Hat, navštivte [zákaznický portál Red Hat](https://access.redhat.com/) a nastavte účet.

* [Rozhraní Azure Command-Line](https://docs.microsoft.com/cli/azure/overview).

* RHEL možnosti – můžete si vybrat mezi průběžnými platbami (PAYG) nebo Přineste si vlastní předplatné (BYOS). V BYOS je před nasazením šablony pro rychlé zprovoznění nutné aktivovat image [Red Hat Cloud Access](https://access.redhat.com/) RHEL Gold.

## <a name="java-ee--jakarta-ee-application-migration"></a>Migrace aplikací z Java EE/Jakarta EE

### <a name="migrate-to-jboss-eap"></a>Migrace na JBoss EAP
JBoss EAP 7,2 a 7,3 jsou certifikované implementace Java Enterprise Edition (Java EE) 8 a Jakarta EE 8 specifikací. JBoss EAP poskytuje předkonfigurované možnosti pro funkce, jako je Clustering s vysokou dostupností (HA), zasílání zpráv a distribuované ukládání do mezipaměti. Umožňuje také uživatelům psát, nasazovat a spouštět aplikace s využitím různých rozhraní API a služeb, které poskytuje JBoss EAP.  Další informace o protokolu EAP JBoss najdete v [úvodu k JBoss protokolu eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html-single/introduction_to_jboss_eap/index) nebo [Úvod do JBoss EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/introduction_to_jboss_eap/index).

 #### <a name="applications-on-jboss-eap"></a>Aplikace na JBoss protokolu EAP

* Webové služby aplikace – webové služby poskytují standardní způsob spolupráce mezi různými softwarovými aplikacemi. Každá aplikace může běžet na různých platformách a architekturách. Tyto webové služby usnadňují interní a heterogenní komunikaci subsystému. Další informace najdete v téma [vývoj aplikací webové služby na základě protokolu eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_web_services_applications/index) nebo [vývoj aplikací webové služby na EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_web_services_applications/index).

* Aplikace EJB (Enterprise Java Fazoles) – EJB 3,2 je rozhraní API pro vývoj distribuovaných, transakčních, zabezpečených a přenosných aplikací pro Java EE a Jakarta et. EJB používá serverové komponenty s názvem Enterprise fazole k implementaci obchodní logiky aplikace v odděleném způsobem, který podporuje opakované použití. Další informace najdete na webu věnovaném [vývoji aplikací EJB na eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_ejb_applications/index) nebo [vývoji aplikací EJB na EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_ejb_applications/index).

* Aplikace v režimu hibernace – vývojáři a správci můžou vyvíjet a nasazovat aplikace/Hibernate pro trvalost rozhraní Java (JPA) s JBoss EAP. Jádro režimu hibernace je objektově-relační mapové rozhraní pro jazyk Java. Poskytuje rozhraní pro mapování objektově orientovaného modelu domény na relační databázi, což umožňuje aplikacím vyhnout se přímé interakci s databází. Správce entit v režimu hibernace implementuje rozhraní pro programování a pravidla pro životní cyklus definovaná [specifikací JPA 2,1](https://www.jcp.org/en/jsr/overview). Spolu s poznámkami k režimu hibernace tato obálka implementuje kompletní řešení JPA (a samostatné), které se nachází na vyspělém jádru hibernace. Další informace o režimu hibernace najdete na [JPA protokolu eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/development_guide/java_persistence_api) nebo  [Jakarta Persistence na EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/development_guide/java_persistence_api).

#### <a name="red-hat-migration-toolkit-for-applications-mta"></a>Sada Red Hat Migration Toolkit for Applications (MTA)
[Sada Red Hat Migration Toolkit for Applications (MTA)](https://developers.redhat.com/products/mta/overview) je migrační nástroj pro aplikační servery Java. Tento nástroj slouží k migraci z jiného aplikačního serveru na JBoss EAP. Funguje s moduly plug-in IDE pro [Prozatmění IDE](https://www.eclipse.org/ide/), [Red Hat CodeReady](https://developers.redhat.com/products/codeready-workspaces/overview)a [Visual Studio Code (vs Code)](https://code.visualstudio.com/docs/languages/java) pro jazyk Java.  MTA je bezplatný a open source nástroj, který bude:
* Automatizace analýzy aplikací
* Podpora odhadu intenzity
* Zrychlení migrace kódu
* Podpora kontejneru
* Integrace s Azure úlohy Builder

### <a name="migrate-jboss-eap-from-on-premises-to-azure"></a>Migrace protokolu EAP JBoss z místního prostředí do Azure
Nabídka Azure Marketplace JBoss EAP v RHEL se nainstaluje a zřídí na virtuálních počítačích Azure za méně než 20 minut. K těmto nabídkám máte přístup z [Azure Marketplace](https://azuremarketplace.microsoft.com/)

Tato nabídka Marketplace obsahuje různé kombinace verzí protokolu EAP a RHEL, které podporují vaše požadavky. JBoss EAP je vždycky vlastní předplatné (BYOS), ale pro RHEL OS si můžete vybrat mezi BYOS nebo průběžnými platbami (PAYG). Nabídka Azure Marketplace zahrnuje možnosti plánu pro JBoss EAP v RHEL jako samostatné nebo clusterované virtuální počítače:

* JBoss EAP 7,2 na virtuálním počítači s RHEL 7,7 (PAYG)
* JBoss EAP 7,2 na virtuálním počítači s RHEL 8,0 (PAYG)
* JBoss EAP 7,3 na virtuálním počítači s RHEL 8,0 (PAYG)
* JBoss EAP 7,2 na virtuálním počítači s RHEL 7,7 (BYOS)
* JBoss EAP 7,2 na virtuálním počítači s RHEL 8,0 (BYOS)
* JBoss EAP 7,3 na virtuálním počítači s RHEL 8,0 (BYOS)

Společně s Azure Marketplace nabídkami jsou k dispozici šablony rychlý Start, které vám umožní začít na cestě k migraci do Azure. Tyto rychlé starty obsahují předem připravené šablony Azure Resource Manager (ARM) a skript pro nasazení JBoss EAP v RHEL v různých konfiguracích a kombinacích verzí. Budete mít následující:

* Load Balancer (kg)
* Privátní IP adresa pro vyrovnávání zatížení a virtuální počítače
* Virtual Network (VNET) s jednou podsítí
* Konfigurace virtuálních počítačů (cluster nebo samostatné)
* Ukázková aplikace Java

Architektura řešení pro tyto šablony zahrnuje:

* JBoss EAP na samostatném virtuálním počítači s RHEL
* JBoss EAP cluster napříč několika virtuálními počítači RHEL
* JBoss EAP cluster pomocí sady škálování virtuálních počítačů Azure

#### <a name="linux-workload-migration-for-jboss-eap"></a>Migrace úloh Linux pro JBoss EAP
Azure pro sestavovatele úloh zjednodušuje proces ověření koncepce, vyhodnocení a migrace místních aplikací Java do Azure. Tvůrce úloh se integruje s nástrojem pro zjišťování Azure Migrate k identifikaci serverů EAP s JBoss. Pak bude dynamicky generovat Ansible PlayBook pro nasazení serveru JBoss EAP. Využívá nástroj pro Red Hat MTA k migraci serverů z jiných aplikačních serverů na JBoss EAP. Mezi kroky pro zjednodušení migrace patří:
* Vyhodnocení – clustery EAP s JBoss s využitím virtuálního počítače Azure nebo sady škálování virtuálních počítačů
* Posouzení – prohledává aplikace a infrastrukturu.
* Konfigurace infrastruktury – vytvoří profil úlohy.
* Nasazení a testování – nasazení, migrace a testování zatížení
* Konfigurace po nasazení – integruje se s daty, monitorováním, zabezpečením, zálohováním a dalšími službami.

## <a name="server-configuration-choice"></a>Volba konfigurace serveru

Pro nasazení virtuálního počítače s RHEL můžete zvolit buď PAYG, nebo BYOS. Obrázky z [Azure Marketplace](https://azuremarketplace.microsoft.com) standardně PAYG. Pokud máte vlastní image RHEL OS, nasaďte virtuální počítač typu BYOS RHEL. Než nasadíte virtuální počítače nebo sadu škálování virtuálního počítače, ujistěte se, že váš účet RHSM má oprávnění BYOS přes cloudový přístup.

JBoss EAP obsahuje výkonné možnosti správy a poskytuje funkce a rozhraní API aplikacím. Tyto možnosti správy se liší v závislosti na použitém operačním režimu ke spuštění protokolu JBoss EAP. Podporuje se na RHEL a ve Windows serveru. JBoss EAP nabízí samostatný serverový operační režim pro správu diskrétních instancí. Nabízí také operační režim spravované domény pro správu skupin instancí z jednoho řídicího bodu. Poznámka: domény spravované JBoss EAP nejsou v Microsoft Azure podporované, protože funkce vysoké dostupnosti (HA) je spravovaná službami infrastruktury Azure. Proměnná prostředí s názvem *EAP_HOME* slouží k označení cesty k instalaci protokolu EAP JBoss.

**Spusťte JBoss EAP jako samostatný server** – následující příkaz vám umožní spustit službu protokolu EAP v samostatném režimu.

```
$EAP_HOME/bin/standalone.sh
```
    
Tento spouštěcí skript používá soubor EAP_HOME/bin/Standalone.conf k nastavení některých výchozích předvoleb, jako jsou například možnosti JVM. Nastavení lze v tomto souboru přizpůsobit. JBoss EAP používá ke spuštění v samostatném režimu konfigurační soubor standalone.xml ve výchozím nastavení, ale můžete ho spustit v jiném režimu. Podrobnosti o dostupných samostatných konfiguračních souborech a způsobu jejich použití najdete v části samostatné [konfigurační soubory serveru pro protokol eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files) nebo [soubory konfigurace samostatného serveru pro protokol EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files) . K zahájení JBoss protokolu EAP s jinou konfigurací použijte argument--Server-config. Například:
    
 ```
 $EAP_HOME/bin/standalone.sh --server-config=standalone-full.xml
 ```
    
Úplný seznam všech dostupných argumentů spouštěcího skriptu a jejich účelů získáte pomocí argumentu--Help nebo v části s argumenty pro modul [runtime serveru v protokolu eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime) nebo v [argumentech modulu runtime serveru v protokolu EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime) .
    
JBoss EAP může fungovat i v režimu clusteru. Další informace najdete v článku Přehled [clusterů s protokolem eap 7,2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuring_messaging/clusters_overview) nebo [ clustery s protokolem EAP 7,3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuring_messaging/clusters_overview) . Zasílání zpráv clusteru JBoss EAP umožňuje seskupení přenosových serverů JBoss EAP ke sdílení zátěže zpracování zpráv. Každý aktivní uzel v clusteru je aktivní JBoss Server pro zasílání zpráv s protokolem EAP, který spravuje vlastní zprávy a zpracovává vlastní připojení.

## <a name="support-and-subscription-notes"></a>Poznámky k podpoře a předplatnému
Tyto šablony pro rychlý Start jsou nabízeny jako: 

- RHEL OS se nabízí jako PAYG nebo BYOS pomocí modelu image Red Hat Gold.
- JBoss EAP se nabízí jenom jako BYOS.

#### <a name="using-rhel-os-with-payg-model"></a>Použití operačního systému RHEL s modelem PAYG

Ve výchozím nastavení tyto šablony pro rychlý Start používají image PAYG na vyžádání RHEL 7,7 nebo 8,0 z Galerie Azure. PAYG image budou mít za běžné náklady na výpočetní výkon, síť a úložiště další poplatky za RHEL předplatné za hodinu. V současné době se instance zaregistruje do vašeho předplatného Red Hat. To znamená, že budete používat jedno z vašich nároků. Tento PAYG obrázek bude mít za následek "dvojitá fakturace". Tomuto problému se můžete vyhnout vytvořením vlastní image RHEL. Pokud se chcete dozvědět víc, přečtěte si článek znalostní báze Red Hat Knowledge Base (KB), [kde se zřídí virtuální počítač s RHEL pro Microsoft Azure](https://access.redhat.com/articles/uploading-rhel-image-to-azure). Nebo aktivujte obrázek [Red Hat Cloud Access](https://access.redhat.com/) RHEL Gold.

Podrobnosti o cenách PAYG virtuálních počítačů najdete v [cenách Red Hat Enterprise Linux](https://azure.microsoft.com/pricing/details/virtual-machines/red-hat/) . Pokud chcete používat RHEL v modelu PAYG, budete potřebovat předplatné Azure se zadaným způsobem platby pro [RHEL 7,7 Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux77-ARM) nebo [RHEL 8,0 Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux80-ARM). Tyto nabídky vyžadují, aby se v předplatném Azure zadal způsob platby.

#### <a name="using-rhel-os-with-byos-model"></a>Použití operačního systému RHEL s modelem BYOS

Pokud chcete používat BYOS pro RHEL operační systém, musíte mít platné předplatné Red Hat s nároky na používání RHEL operačního systému v Azure. Před nasazením operačního systému RHEL s modelem BYOS proveďte následující předpoklady:

1. Ujistěte se, že máte oprávnění RHEL pro operační systém a JBoss EAP připojené k vašemu předplatnému Red Hat.
2. Ověřte ID předplatného Azure, abyste mohli používat image RHEL BYOS. Dokončete proces pomocí [dokumentace správy předplatných Red Hat (RHSM)](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/con-enable-subs) , která zahrnuje tyto kroky:

    2,1 Povolte Microsoft Azure jako poskytovatele na řídicím panelu Red Hat Cloud Access.

    2,2 přidejte své ID předplatného Azure.

    2,3 povolte nové produkty pro cloudový přístup k Microsoft Azure.
    
    2,4 pro vaše předplatné Azure aktivujte image Red Hat Gold. Další informace najdete v kapitole [povolení a udržování předplatných pro cloudový přístup](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure) , kde najdete další podrobnosti.

    2,5 počkejte, než budou k dispozici image Red Hat Gold v předplatném Azure. Tyto zlaté obrázky jsou obvykle k dispozici do 3 hodin od odeslání.
    
3. Přijměte Azure Marketplace podmínky a ujednání pro Image BYOS pro RHEL. Tento proces můžete dokončit spuštěním příkazů rozhraní příkazového řádku (CLI) Azure Command-Line, jak je uvedeno níže. Další informace najdete v dokumentaci k [RHEL BYOS Gold v](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/byos) dokumentaci k Azure, kde najdete další podrobnosti. Je důležité, abyste spustili nejnovější verzi rozhraní příkazového řádku Azure CLI.

    3,1 spustí relaci Azure CLI a ověří se pomocí účtu Azure. Pomoc najdete [v tématu přihlášení pomocí Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli) . Než budete pokračovat, ujistěte se, že používáte nejnovější verzi Azure CLI.

    3,2 Ověřte, že se ve vašem předplatném k dispozici image BYOS RHEL spuštěním následujícího příkazu CLI. Pokud tady žádné výsledky nezískáte, přečtěte si téma #2 a ujistěte se, že je pro Image BYOS RHEL aktivované předplatné Azure.
   
   ```
   az vm image list --offer rhel-byos --all
   ```

    3,3 spusťte následující příkaz, který přijme podmínky Marketplace pro RHEL 7,7 BYOS a RHEL 8,0 BYOS, v uvedeném pořadí.
   ```
   az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm77
   ``` 

   ```
   az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm8
   ``` 
   
4. Vaše předplatné je teď připravené nasazovat RHEL 7,7 nebo 8,0 BYOS na virtuálních počítačích Azure.

#### <a name="using-jboss-eap-with-byos-model"></a>Použití protokolu JBoss EAP s modelem BYOS

Protokol EAP JBoss je k dispozici v Azure pouze prostřednictvím modelu BYOS. Při nasazování této šablony je potřeba zadat přihlašovací údaje RHSM spolu s ID fondu RHSM s platnými nároky EAP. Pokud nemáte nárok na protokol EAP, Získejte před zahájením [zkušební předplatné JBoss EAP](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) .

## <a name="how-to-consume"></a>Jak spotřebovávat

Šablonu můžete nasadit třemi následujícími způsoby:

- Použití PowerShellu – nasaďte šablonu spuštěním následujících příkazů: (informace o instalaci a konfiguraci Azure PowerShell najdete v [Azure PowerShell](https://docs.microsoft.com/powershell/azure/) ).

  ```
  New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri <raw link to the template which can be obtained from github>
  ```
    
- Použití Azure CLI – nasaďte šablonu spuštěním následujících příkazů: (podrobnosti o instalaci a konfiguraci Azure CLI najdete v [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) ).

  ```
  az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  az group deployment create --resource-group <my-resource-group> --template-uri <raw link to the template which can be obtained from github>
  ```

- Použití Azure Portal – nasazení můžete nasadit do Azure Portal pomocí *šablony pro rychlý Start Azure* , jak je uvedeno v následující části. Až budete v rychlém startu, klikněte na tlačítko **nasadit do Azure** nebo **Procházet na GitHubu** .

## <a name="azure-quickstart-templates"></a>Šablony pro rychlý Start Azure

Můžete začít s jednou z šablon rychlého startu JBoss EAP on RHEL, která splňuje váš cíl nasazení. Následuje seznam dostupných šablon pro rychlý Start.

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-standalone-rhel/"> JBoss EAP na RHEL (samostatný virtuální počítač)</a> – tím se nasadí webová aplikace s názvem JBoss-EAP v Azure na JBoss EAP 7,2 nebo 7,3 spuštěná v RHEL 7,7 nebo 8,0 VM.

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-clustered-multivm-rhel/"> JBoss EAP na RHEL (clusterované, multi-VM)</a> – nasadí webovou aplikaci s názvem EAP-Session-Replication v clusteru JBoss EAP 7,2 nebo 7,3, který běží na n čísle RHEL 7,7 nebo 8,0 virtuálních počítačů. Hodnotu n rozhoduje uživatel. Všechny virtuální počítače se přidají do back-endu fondu Load Balancer.

* <a href="https://azure.microsoft.com/en-us/resources/templates/jboss-eap-clustered-vmss-rhel/"> JBoss EAP v RHEL (clusterovaná, služba Virtual Machine Scale set)</a> – nasadí webovou aplikaci s názvem EAP-Session-Replication v clusteru JBoss EAP 7,2 nebo 7,3, na kterém běží na RHEL 7,7 8,0 nebo na instancích sady škálování virtuálních počítačů.

## <a name="resource-links"></a>Odkazy na prostředky:

* [Výhody hybridního využití Azure](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing)
* [Konfigurace aplikace Java pro Azure App Service](https://docs.microsoft.com/azure/app-service/configure-language-java)
* [JBoss EAP na Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [JBoss EAP na Azure App Service Linux](https://docs.microsoft.com/azure/app-service/quickstart-java) Rychlý Start
* [Postup nasazení JBoss EAP do kurzu Azure App Service](https://github.com/JasonFreeberg/jboss-on-app-service)

## <a name="next-steps"></a>Další kroky

* Další informace o [JBoss EAP 7,2](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.2/)
* Další informace o [JBoss EAP 7,3](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.3/)
* Další informace o [správě předplatných Red Hat](https://access.redhat.com/products/red-hat-subscription-management)
* Microsoft docs pro [Red Hat v Azure](https://aka.ms/rhel-docs)
* Nasazení [JBoss EAP na virtuálním počítači s RHEL nebo na základě sady škálování virtuálního počítače z Azure Marketplace](https://aka.ms/AMP-JBoss-EAP)
* Nasazení [JBoss EAP na virtuálním počítači s RHEL/sadu virtuálních počítačů v sadě Azure na úrovni služby pro rychlé](https://aka.ms/Quickstart-JBoss-EAP) zprovoznění
