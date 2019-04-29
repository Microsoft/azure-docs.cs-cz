---
title: Nasazení OpenShift Container Platform samosprávné Marketplace nabídky v Azure | Dokumentace Microsoftu
description: Nasazení OpenShift Container Platform samosprávné Marketplace nabídky v Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/18/2019
ms.author: haroldw
ms.openlocfilehash: 1228c770799de37c85b8a48b1dc923ac8294eeca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60773465"
---
# <a name="configure-prerequisites"></a>Konfigurovat požadavky související s

Před použitím nabídku Marketplace se nasadit cluster samosprávné OpenShift Container Platform v Azure, musí být nakonfigurované některými jeho předpoklady.  Čtení [požadavky Openshiftu](https://docs.microsoft.com/azure/virtual-machines/linux/openshift-prerequisites) článku Pokyny k vytvoření ssh klíč (bez přístupového hesla), služby Azure key vault, tajný kód trezoru klíčů a instanční objekt služby.

 
## <a name="deploy-using-the-marketplace-offer"></a>Nasazení pomocí nabídky Marketplace

Nejjednodušší způsob, jak nasadit cluster samosprávné OpenShift Container Platform v Azure je použít [nabídky Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Tato možnost je nejjednodušší, ale je také má omezené možnosti přizpůsobení. Nabídka Marketplace nasadí OpenShift Container Platform 3.11.82 a zahrnuje následující možnosti konfigurace:

- **Hlavní uzly**: Hlavní uzly tří (3) s konfigurovatelné instancí typu.
- **Infrastruktura uzly**: Tři (3) Infra uzlů se dají konfigurovat instanci typu.
- **Uzly**: Počet uzlů (1 až 9) a typu instance se dají konfigurovat.
- **Typ disku**: Spravované disky se používají.
- **Sítě**: Podpora pro nové nebo stávající sítě a vlastní rozsah CIDR.
- **CNS**: Propojené sítě je možné povolit.
- **Metriky**: Metriky je možné povolit.
- **Protokolování**: Je možné povolit protokolování.
- **Azure Cloud Provider**: Ve výchozím nastavení povolená, je možné zakázat.

V levém horním rohu webu Azure portal klikněte na tlačítko **vytvořit prostředek**, do vyhledávacího pole zadejte "openshift container platform" a stiskněte Enter.

   ![Nové hledání prostředků](media/openshift-marketplace-self-managed/ocp-search.png)  
<br>

Otevře se stránka s výsledky s **Red Hat OpenShift Container Platform Self-Managed** v seznamu. 

   ![Nový výsledek hledání prostředků](media/openshift-marketplace-self-managed/ocp-searchresult.png)  
<br>

Klikněte na tlačítko nabídky v podrobnostech nabídky. Pokud chcete nasadit v rámci této nabídky, klikněte na tlačítko **vytvořit**. Zadejte potřebné parametry uživatelského rozhraní se zobrazí. První obrazovka je **Základy** okno.

   ![Nabízí nadpis stránky](media/openshift-marketplace-self-managed/ocp-titlepage.png)  
<br>

**Základy**

Můžete zobrazit nápovědu pro některý ze vstupních parametrů, najeďte myší ***můžu*** vedle názvu parametru.

Zadejte hodnoty pro vstupní parametry a klikněte na tlačítko **OK**.

| Vstupní parametr | Popis parametru |
|-----------------------|-----------------|
| Název virtuálního počítače uživatele s rolí správce | Správce má být vytvořena na všechny instance virtuálních počítačů |
| SSH veřejný klíč pro uživatele s rolí správce | Veřejný klíč SSH použili k přihlášení do virtuálního počítače – nesmí obsahovat heslo |
| Předplatné | Nasazení clusteru do předplatného Azure |
| Skupina prostředků | Vytvořte novou skupinu prostředků nebo vyberte existující prázdné skupiny prostředků pro prostředky clusteru |
| Location | Oblast Azure pro nasazení do clusteru |

   ![Okno základy nabídky](media/openshift-marketplace-self-managed/ocp-basics.png)  
<br>

**Nastavení infrastruktury**

Zadejte hodnoty pro vstupní parametry a klikněte na tlačítko **OK**.

| Vstupní parametr | Popis parametru |
|-----------------------|-----------------|
| Předpona názvu clusteru OCP | Správce má být vytvořena na všechny instance virtuálních počítačů |
| Velikost hlavního uzlu | Přijměte výchozí velikost virtuálního počítače nebo klikněte na tlačítko **změnit velikost** vybrat jinou velikost virtuálního počítače.  Vyberte odpovídající velikost virtuálního počítače pro pracovní zátěže |
| Velikost uzlu infrastruktury | Přijměte výchozí velikost virtuálního počítače nebo klikněte na tlačítko **změnit velikost** vybrat jinou velikost virtuálního počítače.  Vyberte odpovídající velikost virtuálního počítače pro pracovní zátěže |
| Počet uzlů aplikace | Přijměte výchozí velikost virtuálního počítače nebo klikněte na tlačítko **změnit velikost** vybrat jinou velikost virtuálního počítače.  Vyberte odpovídající velikost virtuálního počítače pro pracovní zátěže |
| Velikost uzlu aplikace | Přijměte výchozí velikost virtuálního počítače nebo klikněte na tlačítko **změnit velikost** vybrat jinou velikost virtuálního počítače.  Vyberte odpovídající velikost virtuálního počítače pro pracovní zátěže |
| Bastion Host velikost | Přijměte výchozí velikost virtuálního počítače nebo klikněte na tlačítko **změnit velikost** vybrat jinou velikost virtuálního počítače.  Vyberte odpovídající velikost virtuálního počítače pro pracovní zátěže |
| Nové nebo existující virtuální sítě | Vytvořit novou virtuální síť (výchozí) nebo použít existující virtuální síť |
| Zvolte výchozí nastavení CIDR nebo upravit rozsah IP (CIDR) | Přijměte výchozí rozsahy CIDR nebo vyberte **vlastní rozsah IP adres** a zadejte informace o vlastním CIDR.  Výchozí nastavení vytvoří virtuální síť s CIDR 10.0.0.0/14, hlavní podsíť s 10.1.0.0/16 infra podsíť s 10.2.0.0/16 a výpočetní výkon a propojenou síť podsíť s 10.3.0.0/16 |
| Název skupina prostředků trezoru klíčů | Název skupiny prostředků obsahující trezor klíčů |
| Název trezoru klíčů | Název služby Key Vault, která obsahuje tajný kód se ssh privátní klíč.  Pouze alfanumerické znaky a pomlčky jsou povolené a být v rozmezí 3 až 24 znaků |
| Název tajného kódu | Název tajného kódu, který obsahuje ssh privátní klíč.  Jsou povoleny pouze alfanumerické znaky a spojovníky |

   ![Nabídka okno infrastruktura](media/openshift-marketplace-self-managed/ocp-inframain.png)  
<br>

**Změnit velikost**

Pokud chcete vybrat jinou velikost virtuálního počítače, klikněte na tlačítko ***změnit velikost***.  Otevře se okno pro výběr VM.  Vyberte velikost virtuálního počítače a klikněte na tlačítko **vyberte**.

   ![Vyberte velikost virtuálního počítače](media/openshift-marketplace-self-managed/ocp-selectvmsize.png)  
<br>

**Existující virtuální sítě**

| Vstupní parametr | Popis parametru |
|-----------------------|-----------------|
| Existující název virtuální sítě | Název existující virtuální síť |
| Název podsítě pro hlavní uzly | Název existující podsítě pro hlavní uzly.  Musí obsahovat alespoň 16 IP adresy a postupujte podle RFC 1918 |
| Název podsítě pro infra uzly | Název existující podsíť pro infra uzly.  Musí obsahovat alespoň 32 IP adresy a postupujte podle RFC 1918 |
| Název podsítě pro výpočetní prostředky a cns uzly | Název existující podsítě pro výpočetní prostředky a cns uzly.  Musí obsahovat alespoň 32 IP adresy a postupujte podle RFC 1918 |
| Skupina prostředků pro existující virtuální sítě. | Název skupiny prostředků, která obsahuje existující virtuální síť |

   ![Nabízejí existující virtuální síť infrastruktury](media/openshift-marketplace-self-managed/ocp-existingvnet.png)  
<br>

**Rozsah vlastní IP adres**

| Vstupní parametr | Popis parametru |
|-----------------------|-----------------|
| Rozsah adres virtuální sítě | Vlastní CIDR pro virtuální síť |
| Rozsah adres podsítě obsahující hlavní uzly | Vlastní CIDR pro hlavní podsítě |
| Rozsah adres podsítě obsahující infrastruktury uzly | Vlastní CIDR podsítě infrastruktury |
| Rozsah adres podsítě, který obsahuje uzly výpočetního výkonu a cns | Vlastní CIDR pro výpočetní prostředky a cns uzly |

   ![Nabízí vlastní rozsah IP infrastruktury](media/openshift-marketplace-self-managed/ocp-customiprange.png)  
<br>

**OpenShift Container Platform**

Zadejte hodnoty pro vstupní parametry a klikněte na tlačítko **OK**

| Vstupní parametr | Popis parametru |
|-----------------------|-----------------|
| Heslo správce Openshiftu | Heslo k počátečnímu uživatelskému OpenShift.  Tento uživatel také bude Správce clusteru |
| Potvrďte heslo správce Openshiftu | Znovu zadejte heslo správce Openshiftu |
| Red Hat správce předplatného uživatelské jméno | Uživatelské jméno pro přístup k předplatného Red Hat nebo ID organizace.  Tento přihlašovací údaj se použije k registraci instance RHEL k vašemu předplatnému a společností Microsoft nebo Red Hat se nebudou ukládat |
| Heslo uživatele Red Hat správce předplatného | Heslo pro přístup k Red Hat předplatného nebo aktivační kód.  Tento přihlašovací údaj se použije k registraci instance RHEL k vašemu předplatnému a společností Microsoft nebo Red Hat se nebudou ukládat |
| ID fondu OpenShift Red Hat správce předplatného | ID fondu, který obsahuje OpenShift Container Platform nárok. Ujistěte se, že máte dostatečná oprávnění OpenShift Container Platform pro instalaci clusteru |
| ID fondu OpenShift Red Hat správce předplatného pro zprostředkovatele / hlavní uzly | Fond ID, které obsahuje OpenShift Container Platform nároky pro zprostředkovatel / hlavní uzly. Ujistěte se, že máte dostatečná oprávnění OpenShift Container Platform pro instalaci clusteru. Pokud nepoužíváte zprostředkovatele / hlavní ID fondu, zadejte ID fondu aplikací uzly |
| Konfigurace poskytovatele cloudu Azure | Nakonfigurujte OpenShift používat poskytovatele cloudu Azure. Nutné v případě, že pomocí Azure disk připojit pro trvalé svazky.  Výchozí hodnota je Yes |
| Identifikátor GUID ID klienta instančního objektu služby AD Azure | Instanční objekt služby AD klienta ID GUID služby Azure – označované také jako ID aplikace. Potřeba pouze v případě konfigurace poskytovatele cloudu Azure nastavena na **Ano** |
| Tajný klíč ID klienta instančního objektu služby AD Azure | Azure AD Service Principal ID tajný kód klienta. Potřeba pouze v případě konfigurace poskytovatele cloudu Azure nastavena na **Ano** |
 
   ![Nabídka okno Openshiftu](media/openshift-marketplace-self-managed/ocp-ocpmain.png)  
<br>

**Další nastavení**

V okně Další nastavení umožňuje konfiguraci CNS glusterfs úložiště, protokolování, metriky a směrovač Sub domény.  Výchozí hodnota nebude instalace některé z těchto možností a použije nip.io jako subdoména směrovač pro účely testování. Povolení CNS nainstaluje tři další výpočetní uzly s tři další připojenými disky, které budou hostiteli glusterfs pody.  

Zadejte hodnoty pro vstupní parametry a klikněte na tlačítko **OK**

| Vstupní parametr | Popis parametru |
|-----------------------|-----------------|
| Konfigurace kontejneru nativní úložiště (CNS) | Nainstaluje CNS OpenShift clusteru a jeho povolení jako úložiště. Bude výchozí, pokud je zakázán zprostředkovatele služby Azure |
| Konfigurace protokolování clusteru | Nainstaluje funkci protokolování EFK do clusteru.  Velikost uzlů odpovídajícím způsobem na hostiteli EFK podů infra |
| Konfigurace metrik pro Cluster | Nainstaluje do clusteru OpenShift Hawkular metriky.  Velikost uzlů odpovídajícím způsobem podů Hawkular metriky hostitele infra |
| Výchozí směrovače subdoména | Vyberte nipio pro účely testování nebo vlastní zadat doménou sub pro produkční prostředí |
 
   ![Nabízí další okna](media/openshift-marketplace-self-managed/ocp-additionalmain.png)  
<br>

**Další nastavení – nadbytečné parametry**

| Vstupní parametr | Popis parametru |
|-----------------------|-----------------|
| (CNS) Velikost uzlu | Přijměte výchozí velikost uzlu, nebo vyberte **změnit velikost** vybrat novou velikost virtuálních počítačů |
| Enter your custom subdomain | Vlastní domény směrování pro vystavení aplikací prostřednictvím směrovače OpenShift clusteru.  Je potřeba vytvořit položku DNS příslušná zástupný znak] |
 
   ![Nabízí další cns instalace](media/openshift-marketplace-self-managed/ocp-additionalcnsall.png)  
<br>

**Souhrn**

V této fázi chcete zkontrolovat, že se kvóta pro jádra je dostačující k nasazení celkový počet virtuálních počítačů vybraných pro cluster dojde k ověření.  Zkontrolujte všechny parametry, které jste zadali.  Pokud vstupy jsou přijatelné, klikněte na tlačítko **OK** pokračujte.

   ![Nabídka okno s přehledem](media/openshift-marketplace-self-managed/ocp-summary.png)  
<br>

**Koupě**

Potvrďte kontaktní údaje najdete na stránce Nákup a kliknutím na **nákupní** přijmout podmínky použití a spuštění nasazení clusteru OpenShift Container Platform.

   ![Nabídky nákupu okno](media/openshift-marketplace-self-managed/ocp-purchase.png)  
<br>


## <a name="connect-to-the-openshift-cluster"></a>Připojte se ke clusteru Openshiftu

Až se nasazení dokončí, získání připojení z výstupní sekce nasazení. Připojení ke konzole OpenShift v prohlížeči pomocí **adresa URL konzoly OpenShift**. Můžete také SSH Bastion host. Tady je příklad, kde je uživatelské jméno správce clusteradmin a bastionu veřejné IP adresy DNS plně kvalifikovaný název domény je bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Použití [odstranění skupiny az](/cli/azure/group) příkazu k odebrání skupiny prostředků, clusteru OpenShift a všechny související prostředky, pokud jste už nepotřebujete.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Další postup

- [Úlohy po nasazení](./openshift-post-deployment.md)
- [Řešení potíží s nasazení OpenShift v Azure](./openshift-troubleshooting.md)
- [Začínáme s OpenShift Container Platform](https://docs.openshift.com/container-platform)

