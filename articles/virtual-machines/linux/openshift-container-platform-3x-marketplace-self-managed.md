---
title: Nasazení OpenShift kontejneru Platform 3,11 Self-Managed nabídky Marketplace v Azure
description: Nasaďte OpenShift Container Platform 3,11 Self-Managed nabídku Marketplace v Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: f257ed1097f49074d70f45f59e9040265f6cedef
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101670436"
---
# <a name="configure-prerequisites"></a>Konfigurovat požadavky

Než začnete používat nabídku Marketplace k nasazení samostatného clusteru OpenShift Container Platform 3,11 v Azure, je nutné nakonfigurovat několik požadavků.  Pokyny k vytvoření klíče SSH (bez hesla) najdete v článku [požadavky na OpenShift](./openshift-container-platform-3x-prerequisites.md) , Trezor klíčů Azure, tajný klíč trezoru klíčů a instanční objekt.

 
## <a name="deploy-using-the-marketplace-offer"></a>Nasazení pomocí nabídky Marketplace

Nejjednodušší způsob, jak nasadit cluster OpenShift Container Platform 3,11 do Azure, je použití [nabídky Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy).

Tato možnost je nejjednodušší, ale má také omezené možnosti přizpůsobení. Nabídka Marketplace nasadí OpenShift kontejnerové platformy 3.11.82 a obsahuje následující možnosti konfigurace:

- **Hlavní uzly**: tři (3) hlavní uzly s konfigurovatelným typem instance.
- **Infračervené uzly**: tři (3) infračervené uzly s konfigurovatelným typem instance.
- **Uzly**: počet uzlů (mezi 1 a 9) a typ instance lze konfigurovat.
- **Typ disku**: Managed disks se používají.
- **Sítě**: podpora pro nové nebo existující sítě a vlastní rozsah CIDR.
- **CNS**: je možné povolit propojené sítě.
- **Metriky**: metriky Hawkular je možné povolit.
- **Protokolování**: protokolování EFK lze povolit.
- **Poskytovatel cloudu Azure**: ve výchozím nastavení povolený může být zakázaný.

V levém horním rohu Azure Portal klikněte na **vytvořit prostředek**, do vyhledávacího pole zadejte ' OpenShift Container Platform ' a stiskněte ENTER.

   ![Nové vyhledávání prostředků](media/openshift-marketplace-self-managed/ocp-search.png)  
<br>

Stránka s výsledky se otevře s **platformou Red Hat OpenShift Container Platform 3,11** , která je v seznamu samostatně spravovaná. 

   ![Výsledek hledání nového prostředku](media/openshift-marketplace-self-managed/ocp-searchresult.png)  
<br>

Kliknutím na nabídku zobrazíte podrobnosti o této nabídce. Chcete-li nasadit tuto nabídku, klikněte na tlačítko **vytvořit**. Zobrazí se uživatelské rozhraní pro zadání potřebných parametrů. První obrazovka je okno **základy** .

   ![Stránka s názvem nabídky](media/openshift-marketplace-self-managed/ocp-titlepage.png)  
<br>

**Základy**

Pokud chcete získat nápovědu k libovolnému vstupnímu parametru, najeďte myší na ***i*** vedle názvu parametru.

Zadejte hodnoty pro vstupní parametry a klikněte na tlačítko **OK**.

| Vstupní parametr | Popis parametru |
|-----------------------|-----------------|
| Uživatelské jméno správce virtuálního počítače | Uživatel s oprávněními správce, který se má vytvořit na všech instancích virtuálních počítačů |
| Veřejný klíč SSH pro uživatele s oprávněními správce | Veřejný klíč SSH, který se používá pro přihlášení k virtuálnímu počítači, nesmí mít přístupové heslo. |
| Předplatné | Předplatné Azure, do kterého se má cluster nasadit |
| Skupina prostředků | Vytvořte novou skupinu prostředků nebo vyberte existující prázdnou skupinu prostředků pro prostředky clusteru. |
| Umístění | Oblast Azure, do které se má cluster nasadit |

   ![Okno základy nabídky](media/openshift-marketplace-self-managed/ocp-basics.png)  
<br>

**Nastavení infrastruktury**

Zadejte hodnoty pro vstupní parametry a klikněte na tlačítko **OK**.

| Vstupní parametr | Popis parametru |
|-----------------------|-----------------|
| OCP – Předpona názvu clusteru | Předpona clusteru používaná ke konfiguraci názvů hostitelů pro všechny uzly. Mezi 1 a 20 znaky |
| Velikost hlavního uzlu | Přijměte výchozí velikost virtuálního počítače nebo klikněte na **změnit velikost** a vyberte jinou velikost virtuálního počítače.  Vyberte vhodnou velikost virtuálního počítače pro vaše pracovní zatížení. |
| Velikost uzlu infrastruktury | Přijměte výchozí velikost virtuálního počítače nebo klikněte na **změnit velikost** a vyberte jinou velikost virtuálního počítače.  Vyberte vhodnou velikost virtuálního počítače pro vaše pracovní zatížení. |
| Počet uzlů aplikace | Přijměte výchozí velikost virtuálního počítače nebo klikněte na **změnit velikost** a vyberte jinou velikost virtuálního počítače.  Vyberte vhodnou velikost virtuálního počítače pro vaše pracovní zatížení. |
| Velikost uzlu aplikace | Přijměte výchozí velikost virtuálního počítače nebo klikněte na **změnit velikost** a vyberte jinou velikost virtuálního počítače.  Vyberte vhodnou velikost virtuálního počítače pro vaše pracovní zatížení. |
| Velikost hostitele bastionu | Přijměte výchozí velikost virtuálního počítače nebo klikněte na **změnit velikost** a vyberte jinou velikost virtuálního počítače.  Vyberte vhodnou velikost virtuálního počítače pro vaše pracovní zatížení. |
| Nové nebo existující Virtual Network | Vytvoření nové virtuální sítě (výchozí) nebo použití existující virtuální sítě |
| Výběr výchozích nastavení CIDR nebo přizpůsobení rozsahu IP adres (CIDR) | Přijměte výchozí rozsahy CIDR nebo vyberte **vlastní rozsah IP adres** a zadejte vlastní informace o CIDR.  Ve výchozím nastavení se vytvoří virtuální síť s CIDR CIDR 10.0.0.0/14, hlavní podsíť s 10.1.0.0/16, podsíť v 10.2.0.0/16 a podsíť COMPUTE a CNS s 10.3.0.0/16. |
| Název skupiny prostředků Key Vault | Název skupiny prostředků, která obsahuje Key Vault |
| Název Key Vault | Název Key Vault, který obsahuje tajný klíč, s privátním klíčem SSH.  Jsou povoleny pouze alfanumerické znaky a spojovníky a musí být v rozmezí 3 až 24 znaků. |
| Název tajného kódu | Název tajného klíče, který obsahuje privátní klíč SSH.  Jsou povoleny pouze alfanumerické znaky a spojovníky. |

   ![Okno nabídky infrastruktury](media/openshift-marketplace-self-managed/ocp-inframain.png)  
<br>

**Změnit velikost**

Pokud chcete vybrat jinou velikost virtuálního počítače, klikněte na ***změnit velikost** _.  Otevře se okno Výběr virtuálního počítače.  Vyberte velikost virtuálního počítače, kterou chcete, a klikněte na _ * vybrat * *.

   ![Vyberte velikost virtuálního počítače.](media/openshift-marketplace-self-managed/ocp-selectvmsize.png)  
<br>

**Existující Virtual Network**

| Vstupní parametr | Popis parametru |
|-----------------------|-----------------|
| Existující název Virtual Network | Název existující virtuální sítě |
| Název podsítě pro hlavní uzly | Název existující podsítě pro hlavní uzly.  Musí obsahovat aspoň 16 IP adres a dodržovat RFC 1918. |
| Název podsítě pro infračervené uzly | Název existující podsítě pro infračervené uzly.  Musí obsahovat aspoň 32 IP adres a musí odpovídat specifikaci RFC 1918. |
| Název podsítě pro uzly COMPUTE a CNS | Název existující podsítě pro uzly COMPUTE a CNS.  Musí obsahovat aspoň 32 IP adres a musí odpovídat specifikaci RFC 1918. |
| Skupina prostředků pro existující Virtual Network | Název skupiny prostředků, která obsahuje existující virtuální síť |

   ![Nabídka existující virtuální sítě infrastruktury](media/openshift-marketplace-self-managed/ocp-existingvnet.png)  
<br>

**Vlastní rozsah IP adres**

| Vstupní parametr | Popis parametru |
|-----------------------|-----------------|
| Rozsah adres pro Virtual Network | Vlastní CIDR pro virtuální síť |
| Rozsah adres pro podsíť obsahující hlavní uzly | Vlastní CIDR pro hlavní podsíť |
| Rozsah adres pro podsíť obsahující uzly infrastruktury | Vlastní CIDR pro podsíť infrastruktury |
| Rozsah adres pro podsíť obsahující uzly COMPUTE a CNS | Vlastní CIDR pro uzly COMPUTE a CNS |

   ![Nabídka vlastní rozsah IP adres infrastruktury](media/openshift-marketplace-self-managed/ocp-customiprange.png)  
<br>

**OpenShift Container Platform 3.11**

Zadejte hodnoty pro vstupní parametry a klikněte na **OK** .

| Vstupní parametr | Popis parametru |
|-----------------------|-----------------|
| Heslo uživatele správce OpenShift | Heslo pro počátečního uživatele OpenShift.  Tento uživatel také bude správcem clusteru. |
| Potvrzení hesla uživatele správce OpenShift | Zadejte znovu heslo uživatele správce OpenShift. |
| Uživatelské jméno správce předplatného Red Hat | Uživatelské jméno pro přístup k vašemu předplatnému Red Hat nebo ID organizace.  Tyto přihlašovací údaje se používají k registraci instance RHEL do předplatného a neuloží se do Microsoftu ani Red Hat. |
| Heslo uživatele Správce předplatného Red Hat | Heslo pro přístup k předplatnému Red Hat nebo aktivačnímu klíči.  Tyto přihlašovací údaje se používají k registraci instance RHEL do předplatného a neuloží se do Microsoftu ani Red Hat. |
| ID fondu OpenShift Správce předplatného Red Hat | ID fondu, které obsahuje nárok na OpenShift kontejnerové platformy Ujistěte se, že máte dostatečné nároky na OpenShift kontejnerové platformy pro instalaci clusteru. |
| ID fondu OpenShift Správce předplatného Red Hat pro zprostředkovatele nebo hlavní uzly | ID fondu, které obsahuje nároky na OpenShift kontejnerové platformy pro uzly pro zprostředkovatele nebo hlavní servery. Ujistěte se, že máte dostatečné nároky na OpenShift kontejnerové platformy pro instalaci clusteru. Pokud nepoužíváte zprostředkovatele nebo ID hlavního fondu, zadejte ID fondu pro uzly aplikace. |
| Konfigurace poskytovatele cloudu Azure | Nakonfigurujte OpenShift pro použití poskytovatele cloudu Azure. Nutné při použití připojení k disku Azure pro trvalé svazky.  Výchozí hodnota je Yes (Ano). |
| Identifikátor GUID ID klienta instančního objektu služby Azure AD | Identifikátor GUID ID klienta instančního objektu služby Azure AD – označuje se také jako AppID. Vyžaduje se jenom v případě, že je konfigurace Azure Cloud Provider nastavená na **Ano** . |
| Tajný kód ID klienta instančního objektu služby Azure AD | Tajný kód ID klienta instančního objektu služby Azure AD. Vyžaduje se jenom v případě, že je konfigurace Azure Cloud Provider nastavená na **Ano** . |
 
   ![Okno nabídky OpenShift](media/openshift-marketplace-self-managed/ocp-ocpmain.png)  
<br>

**Další nastavení**

V okně Další nastavení lze nakonfigurovat GlusterFS pro úložiště, protokolování, metriky a poddoménu směrovače.  Ve výchozím nastavení se neinstaluje žádná z těchto možností a pro účely testování bude používat nip.io jako dílčí doménou směrovače. Když se povolí CNS, nainstaluje se tři dodatečné výpočetní uzly se třemi dalšími připojenými disky, které budou hostovat GlusterFS lusky.  

Zadejte hodnoty pro vstupní parametry a klikněte na **OK** .

| Vstupní parametr | Popis parametru |
|-----------------------|-----------------|
| Konfigurace nativního úložiště kontejneru (CNS) | Nainstaluje v clusteru OpenShift propojené sítě a povolí je jako úložiště. Pokud je poskytovatel Azure zakázaný, bude výchozí. |
| Konfigurace protokolování clusteru | Nainstaluje funkce protokolování EFK do clusteru.  Velikost infračervených uzlů vhodně pro hostování EFKch lusků |
| Konfigurace metrik pro cluster | Nainstaluje metriky Hawkular do clusteru OpenShift.  Velikost infračervených uzlů odpovídajícím způsobem hostování Hawkular metriky |
| Podřízená doména výchozího směrovače | Vyberte nipio pro testování nebo vlastní a zadejte vlastní subdoménu pro produkční prostředí. |
 
   ![Nabídka další okno](media/openshift-marketplace-self-managed/ocp-additionalmain.png)  
<br>

**Další nastavení – další parametry**

| Vstupní parametr | Popis parametru |
|-----------------------|-----------------|
| PROPOJEN Velikost uzlu | Přijměte výchozí velikost uzlu nebo vyberte **změnit velikost** a vyberte novou velikost virtuálního počítače. |
| Zadejte vlastní subdoménu | Vlastní doména směrování, která se má použít k vystavení aplikací přes směrovač v clusteru OpenShift.  Nezapomeňte vytvořit odpovídající položku DNS se zástupnými znaky. |
 
   ![Nabídnout další instalaci propojené sítě](media/openshift-marketplace-self-managed/ocp-additionalcnsall.png)  
<br>

**Souhrn**

K ověření v této fázi dojde, pokud chcete ověřit, jestli je kvóta jader dostatečná pro nasazení celkového počtu virtuálních počítačů vybraných pro cluster.  Zkontrolujte všechny zadané parametry.  Pokud jsou vstupy přijatelné, pokračujte kliknutím na tlačítko **OK** .

   ![Okno souhrnu nabídky](media/openshift-marketplace-self-managed/ocp-summary.png)  
<br>

**Koupě**

Potvrďte kontaktní údaje na stránce koupit a kliknutím na **koupit** přijměte podmínky použití a spusťte nasazení clusteru OpenShift Container Platform.

   ![Okno nákupu nabídky](media/openshift-marketplace-self-managed/ocp-purchase.png)  
<br>


## <a name="connect-to-the-openshift-cluster"></a>Připojení ke clusteru OpenShift

Až se nasazení dokončí, načtěte připojení z části výstup nasazení. Pomocí **adresy URL konzoly OpenShift** se připojte ke konzole OpenShift pomocí prohlížeče. k hostiteli bastionu můžete také přissh. Tady je příklad, kde uživatelské jméno správce je clusteradmin a plně kvalifikovaný název domény DNS bastionu pro veřejnou IP adresu je bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pomocí příkazu [AZ Group Delete](/cli/azure/group) odeberte skupinu prostředků, cluster OpenShift a všechny související prostředky, pokud už je nepotřebujete.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Další kroky

- [Úkoly po nasazení](./openshift-container-platform-3x-post-deployment.md)
- [Řešení potíží s nasazením OpenShift v Azure](./openshift-container-platform-3x-troubleshooting.md)
- [Začínáme s kontejnerovou platformou OpenShift](https://docs.openshift.com)
- 
