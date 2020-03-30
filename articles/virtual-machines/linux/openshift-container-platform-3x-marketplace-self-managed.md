---
title: Nasazení nabídky OpenShift Container Platform 3.11 S vlastním spravovaným marketplace v Azure
description: Nasazení nabídky OpenShift Container Platform 3.11 S vlastním spravovaným marketplace v Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 235efd746562ea4bd52b9cb57da0d8165d60de02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75561316"
---
# <a name="configure-prerequisites"></a>Konfigurace požadavků

Před použitím nabídky Marketplace k nasazení clusteru OpenShift Container Platform 3.11 s vlastním názvem v Azure musí být nakonfigurovaných několik předpokladů.  Přečtěte si článek [o požadavcích na předpoklady OpenShift,](https://docs.microsoft.com/azure/virtual-machines/linux/openshift-container-platform-3x-prerequisites) kde najdete pokyny k vytvoření klíče ssh (bez přístupové fráze), trezoru klíčů Azure, tajného klíče trezoru klíčů a instančního objektu služby.

 
## <a name="deploy-using-the-marketplace-offer"></a>Nasazení pomocí nabídky Marketplace

Nejjednodušší způsob nasazení clusteru OpenShift Container Platform 3.11 s vlastním správou do Azure je použít [nabídku Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy).

Tato možnost je nejjednodušší, ale má také omezené možnosti přizpůsobení. Nabídka Marketplace nasazuje platformu OpenShift Container Platform 3.11.82 a obsahuje následující možnosti konfigurace:

- **Hlavní uzly**: Tři (3) hlavní uzly s konfigurovatelným typem instance.
- **Infranozy**: Tři (3) Infra nodes s konfigurovatelným typem instance.
- **Uzly**: Počet uzlů (mezi 1 a 9) a typ instance jsou konfigurovatelné.
- **Typ disku**: Používají se spravované disky.
- **Networking**: Podpora nové nebo stávající sítě a vlastní řady CIDR.
- **CNS**: CNS může být povolena.
- **Metriky:** Hawkular metriky mohou být povoleny.
- **Protokolování**: Protokolování EFK lze povolit.
- **Azure Cloud Provider**: Ve výchozím nastavení je povoleno, může být zakázáno.

V levém horním rohu portálu Azure klikněte na **Vytvořit prostředek**, do vyhledávacího pole zadejte "openshift container platform" a klikněte na Enter.

   ![Hledání nových zdrojů](media/openshift-marketplace-self-managed/ocp-search.png)  
<br>

Stránka Výsledky se otevře v seznamu s **vlastní spravovanou platformou Red Hat OpenShift Container Platform 3.11.** 

   ![Výsledek hledání nových zdrojů](media/openshift-marketplace-self-managed/ocp-searchresult.png)  
<br>

Kliknutím na nabídku zobrazíte podrobnosti o nabídce. Chcete-li tuto nabídku nasadit, klepněte na tlačítko **Vytvořit**. Zobrazí se ui pro zadání potřebných parametrů. První obrazovka je okno **Základy.**

   ![Stránka s názvem nabídky](media/openshift-marketplace-self-managed/ocp-titlepage.png)  
<br>

**Základy**

Chcete-li získat pomoc na některý ze vstupních parametrů, najeďte nad ***i*** vedle názvu parametru.

Zadejte hodnoty vstupních parametrů a klepněte na **OK**.

| Vstupní parametr | Popis parametru |
|-----------------------|-----------------|
| Uživatelské jméno správce virtuálního účtu | Uživatel správce, který má být vytvořen ve všech instancích virtuálních počítačů |
| Veřejný klíč SSH pro uživatele správce | Veřejný klíč SSH používaný k přihlášení k virtuálnímu virtuálnímu mísu – nesmí mít přístupové heslo |
| Předplatné | Předplatné Azure pro nasazení clusteru do |
| Skupina prostředků | Vytvoření nové skupiny prostředků nebo výběr existující prázdné skupiny prostředků pro prostředky clusteru |
| Umístění | Oblast Azure pro nasazení clusteru do |

   ![Nabídka základů čepele](media/openshift-marketplace-self-managed/ocp-basics.png)  
<br>

**Nastavení infrastruktury**

Zadejte hodnoty vstupních parametrů a klepněte na **OK**.

| Vstupní parametr | Popis parametru |
|-----------------------|-----------------|
| Předpona názvu clusteru OCP | Předpona clusteru slouží ke konfiguraci názvů hostitelů pro všechny uzly. Mezi 1 a 20 znaky |
| Velikost hlavního uzlu | Přijměte výchozí velikost virtuálního počítače nebo klikněte na **Změnit velikost** a vyberte jinou velikost virtuálního počítače.  Výběr vhodné velikosti virtuálního počítače pro pracovní zatížení |
| Velikost uzlu infrastruktury | Přijměte výchozí velikost virtuálního počítače nebo klikněte na **Změnit velikost** a vyberte jinou velikost virtuálního počítače.  Výběr vhodné velikosti virtuálního počítače pro pracovní zatížení |
| Počet uzlů aplikace | Přijměte výchozí velikost virtuálního počítače nebo klikněte na **Změnit velikost** a vyberte jinou velikost virtuálního počítače.  Výběr vhodné velikosti virtuálního počítače pro pracovní zatížení |
| Velikost uzlu aplikace | Přijměte výchozí velikost virtuálního počítače nebo klikněte na **Změnit velikost** a vyberte jinou velikost virtuálního počítače.  Výběr vhodné velikosti virtuálního počítače pro pracovní zatížení |
| Velikost hostitele bašty | Přijměte výchozí velikost virtuálního počítače nebo klikněte na **Změnit velikost** a vyberte jinou velikost virtuálního počítače.  Výběr vhodné velikosti virtuálního počítače pro pracovní zatížení |
| Nová nebo existující virtuální síť | Vytvoření nové virtuální sítě (výchozí) nebo použití existující virtuální sítě |
| Zvolte výchozí nastavení CIDR nebo přizpůsobit rozsah IP (CIDR) | Přijměte výchozí rozsahy CIDR nebo vyberte **vlastní rozsah IP adres** a zadejte vlastní informace CIDR.  Výchozí nastavení vytvoří virtuální síť s CIDR 10.0.0.0/14, hlavní podsíť s 10.1.0.0/16, infrapodsíť s 10.2.0.0/16 a výpočetní a cns podsíť s 10.3.0.0/16 |
| Název skupiny prostředků trezoru klíčů | Název skupiny prostředků obsahující trezor klíčů |
| Název trezoru klíčů | Název trezoru klíčů, který obsahuje tajný klíč s ssh soukromý klíč.  Jsou povoleny pouze alfanumerické znaky a pomlčky, které mají 3 až 24 znaků. |
| Tajný název | Název tajného klíče, který obsahuje ssh soukromý klíč.  Povoleny jsou pouze alfanumerické znaky a pomlčky. |

   ![Nabídka lopatek infrastruktury](media/openshift-marketplace-self-managed/ocp-inframain.png)  
<br>

**Změna velikosti**

Pokud chcete vybrat jinou velikost virtuálního počítače, klikněte na ***Změnit velikost***.  Otevře se okno pro výběr virtuálního zařízení.  Vyberte požadovanou velikost virtuálního počítače a klepněte na **vybrat**.

   ![Vybrat velikost virtuálního počítače](media/openshift-marketplace-self-managed/ocp-selectvmsize.png)  
<br>

**Existující virtuální síť**

| Vstupní parametr | Popis parametru |
|-----------------------|-----------------|
| Název existující virtuální sítě | Název existující virtuální sítě |
| Název podsítě pro hlavní uzly | Název existující podsítě pro hlavní uzly.  Musí obsahovat alespoň 16 IP adres a sledovat RFC 1918 |
| Název podsítě pro infranozy | Název existující podsítě pro infranozuly.  Musí obsahovat alespoň 32 IP adres a sledovat RFC 1918 |
| Název podsítě pro výpočetní a cns uzly | Název existující podsítě pro výpočetní a cns uzly.  Musí obsahovat alespoň 32 IP adres a sledovat RFC 1918 |
| Skupina prostředků pro existující virtuální síť | Název skupiny prostředků, která obsahuje existující virtuální síť |

   ![Nabídka existující infrastruktury virtuální sítě](media/openshift-marketplace-self-managed/ocp-existingvnet.png)  
<br>

**Vlastní rozsah IP adres**

| Vstupní parametr | Popis parametru |
|-----------------------|-----------------|
| Rozsah adres pro virtuální síť | Vlastní CIDR pro virtuální síť |
| Rozsah adres pro podsíť obsahující hlavní uzly | Vlastní CIDR pro hlavní podsíť |
| Rozsah adres pro podsíť obsahující uzly infrastruktury | Vlastní CIDR pro podsíť infrastruktury |
| Rozsah adres pro podsíť obsahující výpočetní uzly a uzly cns | Vlastní CIDR pro výpočetní a cns uzly |

   ![Nabídka vlastní hospo- oblasti IP infrastruktury](media/openshift-marketplace-self-managed/ocp-customiprange.png)  
<br>

**OpenShift Container Platform 3.11**

Zadejte hodnoty vstupních parametrů a klepněte na **OK.**

| Vstupní parametr | Popis parametru |
|-----------------------|-----------------|
| Uživatelské heslo správce OpenShift | Heslo pro počátečního uživatele OpenShift.  Tento uživatel bude také správcem clusteru. |
| Potvrdit heslo uživatele openshiftadmin | Znovu zadejte uživatelské heslo správce OpenShiftu |
| Uživatelské jméno Správce předplatného Red Hat | Uživatelské jméno pro přístup k předplatnému Red Hat nebo ID organizace.  Toto pověření se používá k registraci instance RHEL do vašeho předplatného a nebude uloženo společností Microsoft nebo Red Hat. |
| Uživatelské heslo správce předplatného Red Hat | Heslo pro přístup k předplatnému nebo aktivačnímu klíči Red Hat.  Toto pověření se používá k registraci instance RHEL do vašeho předplatného a nebude uloženo společností Microsoft nebo Red Hat. |
| Red Hat Správce předplatného OpenShift ID fondu | ID fondu, který obsahuje oprávnění platformy OpenShift Container Platform. Ujistěte se, že máte dostatek nároků na Platformu kontejnerů OpenShift pro instalaci clusteru |
| Red Hat Subscription Manager OpenShift ID fondu pro Broker / Hlavní uzly | ID fondu, který obsahuje nároky platformy OpenShift Container Platform pro broker / hlavní uzly. Ujistěte se, že máte dostatek nároků OpenShift Kontejner platformy pro instalaci clusteru. Pokud nepoužíváte ID brokera / hlavního fondu, zadejte ID fondu pro uzly aplikací. |
| Konfigurace poskytovatele Cloudu Azure | Nakonfigurujte OpenShift tak, aby používal Azure Cloud Provider. Nezbytné, pokud používáte disk Azure připojit pro trvalé svazky.  Výchozí hodnota je Ano |
| Identifikátor GUID hlavního klienta služby Azure AD | Identifikátor GUID id klienta hlavního servisního objektu Azure AD – označovaný také jako AppID. Potřeba jenom v případě, že konfigurace Azure Cloud Provider nastavena na **Ano** |
| Tajný klíč ID klienta hlavního klienta služby Azure AD | Tajný klíč ID hlavního klienta služby Azure AD. Potřeba jenom v případě, že konfigurace Azure Cloud Provider nastavena na **Ano** |
 
   ![Nabídka čepele OpenShift](media/openshift-marketplace-self-managed/ocp-ocpmain.png)  
<br>

**Další nastavení**

Okno Další nastavení umožňuje konfiguraci cns pro úložiště glusterfs, protokolování, metriky a dílčí doménu směrovače.  Výchozí nastavení nenainstaluje žádnou z těchto možností a použije nip.io jako subdoménu směrovače pro účely testování. Povolení CNS nainstaluje tři další výpočetní uzly se třemi dalšími připojenými disky, které budou hostovat pody glusterfs.  

Zadejte hodnoty vstupních parametrů a klepněte na **OK.**

| Vstupní parametr | Popis parametru |
|-----------------------|-----------------|
| Konfigurace nativního úložiště kontejnerů (CNS) | Nainstaluje CNS do clusteru OpenShift a povolí jej jako úložiště. Bude výchozí, pokud je azure provider zakázáno |
| Konfigurace protokolování clusteru | Nainstaluje do clusteru funkci protokolování EFK.  Velikost infrauzlů vhodně pro hostování podů EFK |
| Konfigurace metrik pro cluster | Nainstaluje hawkular metriky do clusteru OpenShift.  Velikost infrauzlů vhodně pro hostování podů hawkular metriky |
| Výchozí subdoména směrovače | Vyberte nipio pro testování nebo vlastní pro vstup do vlastní sub domény pro produkční prostředí |
 
   ![Nabídněte další čepel](media/openshift-marketplace-self-managed/ocp-additionalmain.png)  
<br>

**Další nastavení – další parametry**

| Vstupní parametr | Popis parametru |
|-----------------------|-----------------|
| (CNS) Velikost uzlu | Přijetí výchozí velikosti uzlu nebo výběrem **možnosti Změnit velikost** vyberte novou velikost virtuálního počítače. |
| Zadání vlastní subdomény | Vlastní směrovací doména, která má být použita pro vystavení aplikací prostřednictvím směrovače v clusteru OpenShift.  Nezapomeňte vytvořit příslušnou položku DNS se zástupnými symboly] |
 
   ![Nabídka další cns Instalace](media/openshift-marketplace-self-managed/ocp-additionalcnsall.png)  
<br>

**Souhrn**

Ověření dochází v této fázi ke kontrole kvóty jádra je dostatečná k nasazení celkový počet virtuálních disek vybraných pro cluster.  Zkontrolujte všechny zadané parametry.  Pokud jsou vstupy přijatelné, pokračujte klepnutím na **tlačítko OK.**

   ![Nabídka souhrnného nože](media/openshift-marketplace-self-managed/ocp-summary.png)  
<br>

**Koupě**

Potvrďte kontaktní informace na stránce Koupit a kliknutím na **Koupit** přijměte podmínky použití a spusťte nasazení clusteru platformy OpenShift Container Platform.

   ![Nabídka nákupního kotouče](media/openshift-marketplace-self-managed/ocp-purchase.png)  
<br>


## <a name="connect-to-the-openshift-cluster"></a>Připojení ke clusteru OpenShift

Po dokončení nasazení načtěte připojení z výstupní části nasazení. Připojte se ke konzoli OpenShift s prohlížečem pomocí **adresy URL konzoly OpenShift**. můžete také SSH na hostitele Bastion. Následuje příklad, kdy je uživatelské jméno správce clusteradmin a je bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com veřejný název domény dns DNS bastion:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pomocí příkazu [delete skupiny az](/cli/azure/group) odeberte skupinu prostředků, cluster OpenShift a všechny související prostředky, pokud už nejsou potřeba.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Další kroky

- [Úlohy po nasazení](./openshift-container-platform-3x-post-deployment.md)
- [Poradce při potížích s nasazením OpenShift v Azure](./openshift-container-platform-3x-troubleshooting.md)
- [Začínáme s platformou OpenShift Container Platform](https://docs.openshift.com)
- 