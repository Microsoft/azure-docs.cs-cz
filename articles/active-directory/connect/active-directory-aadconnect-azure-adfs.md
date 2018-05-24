---
title: Služba AD FS (Active Directory Federation Services) v Azure | Microsoft Docs
description: V tomto dokumentu se dozvíte, jak nasadit služby AD FS v Azure a zajistit vysokou dostupnost.
keywords: nasazení AD FS v Azure, nasazení Azure AD FS, Azure AD FS, Azure ADFS, nasazení AD FS, nasazení AD FS, AD FS v Azure, nasazení ADFS v Azure, nasazení AD FS v Azure, AD FS Azure, úvod do služby AD FS, Azure, AD FS v Azure, IAAS, ADFS, přesunutí AD FS do Azure
services: active-directory
documentationcenter: ''
author: anandyadavmsft
manager: mtillman
editor: ''
ms.assetid: 692a188c-badc-44aa-ba86-71c0e8074510
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/17/2017
ms.author: anandy; billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 76ed05d55389e2c05b38fe1f2c239f544c6a5d38
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="deploying-active-directory-federation-services-in-azure"></a>Nasazení služby AD FS (Active Directory Federation Service) v Azure
Služby AD FS nabízí zjednodušené možnosti zabezpečené federace identit a jednotného přihlašování na webu (SSO). Federace pomocí Azure AD nebo O365 uživatelům umožňuje ověřování pomocí místních přihlašovacích údajů a přístup ke všem prostředkům v cloudu. V důsledku toho je důležité mít vysoce dostupnou infrastrukturu služby AD FS, která zajistí přístup k místním prostředkům i k prostředkům v cloudu. Nasazení služby AD FS v Azure může zajistit požadovanou vysokou dostupnost při minimálním úsilí.
Níže uvádíme některé z řady výhod, které nasazení služby AD FS v Azure přináší:

* **Vysoká dostupnost** – výkon skupin dostupnosti Azure vám zajistí vysokou dostupnost infrastruktury.
* **Jednoduché škálování** – potřebujete více výkonu? Pomocí několika kliknutí můžete v Azure snadno migrovat na výkonnější počítače.
* **Redundance mezi geografickými lokalitami** – s geografickou redundancí Azure se můžete spolehnout na vysokou dostupnost infrastruktury po celém světě.
* **Snadná správa** – velmi zjednodušené možnosti správy na portálu Azure nabízejí snadnou a bezproblémovou správu infrastruktury. 

## <a name="design-principles"></a>Principy návrhu
![Návrh nasazení](./media/active-directory-aadconnect-azure-adfs/deployment.png)

Výše uvedený diagram zobrazuje doporučenou základní topologii, podle které můžete začít nasazovat infrastrukturu služby AD FS v Azure. Principy, na kterých stojí různé součásti topologie, jsou uvedeny níže:

* **Řadič domény a servery služby AD FS**: Pokud máte méně než 1000 uživatelů, můžete roli služby AD FS jednoduše nainstalovat na řadiče domény. Pokud nechcete ovlivnit výkon řadičů domény nebo pokud máte více než 1000 uživatelů, potom službu AD FS nasaďte na samostatné servery.
* **Server WAP** – je nutné nasadit proxy servery webových aplikací, aby se uživatelé mohli spojit se službou AD FS i když jsou mimo síť společnosti.
* **DMZ**: Proxy servery webových aplikací budou umístěny v zóně DMZ a mezi zónou DMZ a interní podsítí je povolený přístup jenom prostřednictvím protokolu TCP a portu 443.
* **Nástroje pro vyrovnávání zatížení**: Pokud chcete zajistit vysokou dostupnost služby AD FS a proxy serverů webových aplikací, doporučujeme na serverech služby AD FS používat interní nástroj pro vyrovnávání zatížení a na proxy serverech webových aplikací zase službu Azure Load Balancer.
* **Skupiny dostupnosti**: Pokud chcete zajistit redundanci pro nasazení služby AD FS, doporučujeme v případě podobných zatížení seskupit dva nebo více virtuálních počítačů do skupiny dostupnosti. Tato konfigurace zajišťuje, aby během plánované nebo neplánované události údržby zůstal dostupný alespoň jeden virtuální počítač.
* **Účty úložiště**: Je doporučeno mít dva účty úložiště. Pokud máte jen jeden účet úložiště, může se takový účet stát jediným bodem selhání a může způsobit nedostupnost nasazení v nepravděpodobném scénáři, kdy se účet úložiště ocitne mimo provoz. Když budete mít dva účty úložiště, můžete ke každé chybové linii přidružit jeden účet úložiště.
* **Oddělení sítí**: Proxy servery webových aplikací musí být nasazené v samostatné síti DMZ. Jednu virtuální síť můžete rozdělit do dvou podsítí a potom můžete proxy servery webových aplikací nasadit v izolované podsíti. Nastavení skupiny zabezpečení sítě můžete jednoduše nakonfigurovat pro každou podsíť a potom mezi nimi povolte jenom požadovanou komunikaci. Další podrobnosti jsou popsány v níže uvedeném scénáři nasazení.

## <a name="steps-to-deploy-ad-fs-in-azure"></a>Postup nasazení služby AD FS v Azure
Kroky uvedené v této části popisují postup nasazení níže znázorněné infrastruktury služby AD FS v Azure.

### <a name="1-deploying-the-network"></a>1. Nasazení sítě
Jak je uvedeno výše, můžete buď vytvořit dvě podsítě v jedné virtuální síti, nebo můžete vytvořit dvě zcela odlišné virtuální sítě (VNet). Tento článek se zaměří na nasazení jedné virtuální sítě a její rozdělení na dvě podsítě. Tento způsob je snazší, protože dvě samostatné virtuální sítě by pro komunikaci vyžadovaly bránu VNnet To VNet.

**1.1 Vytvoření virtuální sítě**

![Vytvoření virtuální sítě](./media/active-directory-aadconnect-azure-adfs/deploynetwork1.png)

Na portálu Azure vyberte virtuální síť. Jedním kliknutím můžete virtuální síť a jednu podsíť okamžitě nasadit. Podsíť INT je také definována a připravená na přidání virtuálních počítačů.
Dalším krokem je přidání další podsítě do sítě, tj. podsítě DMZ. Pokud chcete vytvořit podsíť DMZ, stačí provést následující:

* vyberte nově vytvořenou síť,
* ve vlastnostech vyberte podsíť,
* na panelu podsítě klikněte na tlačítko Přidat,
* zadejte název podsítě a údaje o adresním prostoru, které jsou k vytvoření podsítě potřeba.

![Podsíť](./media/active-directory-aadconnect-azure-adfs/deploynetwork2.png)

![Podsíť DMZ](./media/active-directory-aadconnect-azure-adfs/deploynetwork3.png)

**1.2. Vytvoření skupin zabezpečení sítě**

Skupina zabezpečení sítě (NSG) obsahuje seznam pravidel seznamu řízení přístupu (ACL), která instancím virtuálních počítačů ve službě Virtual Network povolují nebo odpírají síťový provoz. Skupiny NSG můžou být přidružené buď k podsítím, nebo k jednotlivým instancím virtuálních počítačů v této podsíti. Pokud je skupina zabezpečení sítě přidružená k podsíti, pravidla seznamu ACL platí pro všechny instance virtuálních počítačů v této podsíti.
Pro potřeby těchto pokynů vytvoříme dvě skupiny zabezpečení sítě – jednu pro interní síť a jednu pro DMZ. Budou označeny NSG_INT a NSG_DMZ.

![Vytvoření skupiny zabezpečení sítě](./media/active-directory-aadconnect-azure-adfs/creatensg1.png)

Po vytvoření skupiny zabezpečení sítě bude 0 příchozích a 0 odchozích pravidel. Jakmile budou role na příslušných serverech nainstalované a funkční, můžete v závislosti na požadované úrovni zabezpečení vytvořit příchozí a odchozí pravidla.

![Inicializace skupiny zabezpečení sítě](./media/active-directory-aadconnect-azure-adfs/nsgint1.png)

Po vytvoření skupin zabezpečení sítě přidružte NSG_INT k podsíti INT a NSG_DMZ k podsíti DMZ. Níže je uvedený snímek obrazovky s příkladem:

![Konfigurace skupiny zabezpečení sítě](./media/active-directory-aadconnect-azure-adfs/nsgconfigure1.png)

* Kliknutím na Podsítě otevřete panel podsítí.
* Vyberte podsíť, kterou chcete přidružit ke skupině zabezpečení sítě. 

Po konfiguraci by měl panel podsítí vypadat následovně:

![Panel Podsítě po přidružení ke skupinám zabezpečení sítě](./media/active-directory-aadconnect-azure-adfs/nsgconfigure2.png)

**1.3. Vytvoření připojení k místnímu systému**

Abychom mohli nasadit řadič domény (DC) v Azure, budeme potřebovat připojení k místnímu systému. Azure nabízí různé možnosti propojení místní infrastruktury s infrastrukturou Azure.

* Point-to-Site
* Virtuální síť s připojením typu Site-to-Site
* ExpressRoute

Doporučujeme použít ExpressRoute. ExpressRoute vám umožňuje vytvářet privátní připojení mezi datovými centry Azure a infrastrukturou, která se nachází ve vašem umístění nebo v prostředí ve společném umístění. Připojení ExpressRoute se nepřenášejí prostřednictvím veřejného internetu. Nabízejí větší spolehlivost, vyšší rychlost, nižší latenci a vyšší zabezpečení než typická připojení přes internet.
Přestože doporučujeme používat ExpressRoute, můžete si zvolit jakoukoli metodu připojení, která vaší organizaci vyhovuje. Další informace o ExpressRoute a různých možnostech připojení pomocí ExpressRoute najdete v článku [Technický přehled ExpressRoute](https://aka.ms/Azure/ExpressRoute).

### <a name="2-create-storage-accounts"></a>2. Vytvoření účtů úložiště
Abyste udrželi vysokou dostupnost a vyhnuli se závislost na jednom účtu úložiště, můžete si vytvořit dva účty úložiště. Rozdělte počítače v každé skupině dostupnosti do dvou skupin a potom každé skupině přiřaďte samostatný účet úložiště.

![Vytvoření účtů úložiště](./media/active-directory-aadconnect-azure-adfs/storageaccount1.png)

### <a name="3-create-availability-sets"></a>3. Vytvoření skupin dostupnosti
Pro každou roli (řadič domény/AD FS a WAP) vytvořte skupiny dostupnosti tak, aby každá obsahovala minimálně dva počítače. Tím dosáhnete vyšší dostupnost pro každou roli. Při vytváření skupin dostupnosti je nezbytné rozhodnout o následujícím:

* **Domény selhání**: Virtuální počítače ve stejné doméně selhání sdílejí stejný zdroj napájení a fyzický síťový přepínač. Jako minimum doporučujeme dvě domény selhání. Výchozí hodnota je tři a pro potřeby tohoto nasazení to tak můžete nechat.
* **Aktualizační domény**: Počítače, které patří do stejné aktualizační domény, se během aktualizace restartují společně. Jako minimum doporučujeme dvě aktualizační domény. Výchozí hodnota je pět a pro potřeby tohoto nasazení to tak můžete nechat.

![Skupiny dostupnosti](./media/active-directory-aadconnect-azure-adfs/availabilityset1.png)

Vytvoření následujících skupin dostupnosti

| Skupina dostupnosti | Role | Domény selhání | Aktualizační domény |
|:---:|:---:|:---:|:--- |
| contosodcset |DC/ADFS |3 |5 |
| contosowapset |WAP |3 |5 |

### <a name="4-deploy-virtual-machines"></a>4. Nasazení virtuálních počítačů
Dalším krokem je nasazení virtuálních počítačů, které budou hostiteli různých rolí ve vaší infrastruktuře. Jako minimum doporučujeme dva počítače v každé skupině dostupnosti. Vytvořte čtyři virtuální počítače pro základní nasazení.

| Počítač | Role | Podsíť | Skupina dostupnosti | Účet úložiště | IP adresa |
|:---:|:---:|:---:|:---:|:---:|:---:|
| contosodc1 |DC/ADFS |INT |contosodcset |contososac1 |Statická |
| contosodc2 |DC/ADFS |INT |contosodcset |contososac2 |Statická |
| contosowap1 |WAP |DMZ |contosowapset |contososac1 |Statická |
| contosowap2 |WAP |DMZ |contosowapset |contososac2 |Statická |

Pravděpodobně jste si všimli, že jsme nezadali žádnou skupinu zabezpečení sítě. Důvodem je to, že Azure umožňuje používání skupin zabezpečení sítě na úrovni podsítě. Potom můžete řídit síťový provoz počítačů pomocí jednotlivých skupin zabezpečení sítě, které jsou přidružené buď k podsíti, nebo k objektu síťové karty. Další informace si můžete přečíst v článku [Skupina zabezpečení sítě (NSG)](https://aka.ms/Azure/NSG).
Pokud spravujete DNS, doporučujeme statickou IP adresu. Můžete použít Azure DNS a v záznamech DNS domény můžete odkazovat na nové počítače podle jejich plně kvalifikovaných názvů domén v Azure.
Po dokončení nasazení by mělo podokno virtuálního počítače vypadat následovně:

![Nasazené virtuální počítače](./media/active-directory-aadconnect-azure-adfs/virtualmachinesdeployed_noadfs.png)

### <a name="5-configuring-the-domain-controller--ad-fs-servers"></a>5. Konfigurace řadiče domény a serverů služby AD FS
 Abyste mohli ověřit jakékoli příchozí žádosti, musí služba AD FS kontaktovat řadič domény. Pokud chcete ušetřit nákladné spojení z Azure do řadiče místní domény kvůli ověřování, doporučujeme v Azure nasadit repliku řadiče domény. Abyste dosáhli vysoké dostupnosti, doporučujeme vytvořit skupinu dostupnosti s alespoň dvěma řadiči domény.

| Řadič domény | Role | Účet úložiště |
|:---:|:---:|:---:|
| contosodc1 |Replika |contososac1 |
| contosodc2 |Replika |contososac2 |

* Povýšení dvou serverů na repliky řadičů domény s DNS
* Nakonfigurujte servery služby AD FS tím, že pomocí správce serveru nainstalujete roli služby AD FS.

### <a name="6-deploying-internal-load-balancer-ilb"></a>6. Nasazení interního nástroje pro vyrovnávání zatížení (ILB)
**6.1. Vytvoření interního nástroje pro vyrovnávání zatížení**

Pokud chcete nasadit interní nástroj pro vyrovnávání zatížení, vyberte na portálu Azure možnost Nástroje pro vyrovnávání zatížení a klikněte na Přidat (+).

> [!NOTE]
> Pokud se v nabídce nezobrazí **Nástroje pro vyrovnávání zatížení**, klikněte v levém dolním rohu portálu na tlačítko **Procházet** a pomocí posuvníku přejděte k možnosti **Nástroje pro vyrovnávání zatížení**.  Potom ji kliknutím na žlutou hvězdičku přidejte do nabídky. Vyberte ikonu nového nástroje pro vyrovnávání zatížení a otevřete panel, pomocí kterého můžete začít s konfigurací nástroje.
> 
> 

![Vyhledání nástroje pro vyrovnávání zatížení](./media/active-directory-aadconnect-azure-adfs/browseloadbalancer.png)

* **Název**: Dejte nástroji pro vyrovnávání zatížení libovolný vhodný název.
* **Schéma**: Vzhledem k tomu, že tento nástroj pro vyrovnávání zatížení bude umístěn před servery služby AD FS a je určen JENOM pro interní síťová připojení, vyberte Interní.
* **Virtuální síť**: Zvolte virtuální síť, do které službu AD FS nasazujete.
* **Podsíť**: Tady zvolte interní podsíť.
* **Přiřazení IP adresy**: Statické.

![Interní nástroj pro vyrovnávání zatížení](./media/active-directory-aadconnect-azure-adfs/ilbdeployment1.png)

Po kliknutí na tlačítko Vytvořit a po nasazení interního nástroje pro vyrovnávání zatížení byste měli nástroj vidět v seznamu nástrojů pro vyrovnávání zatížení:

![Nástroje pro vyrovnávání zatížení, po nasazení interního nástroje pro vyrovnávání zatížení](./media/active-directory-aadconnect-azure-adfs/ilbdeployment2.png)

Dalším krokem je konfigurace back-endového fondu a back-endového testu.

**6.2. Konfigurace back-endového fondu interního nástroje pro vyrovnávání zatížení**

Na panelu nástrojů pro vyrovnávání zatížení vyberte nově vytvořený nástroj. Otevře se panel nastavení. 

1. Výběr back-endových fondů na panelu nastavení
2. Na panelu Přidání back-endového fondu klikněte na Přidat virtuální počítač.
3. Zobrazí se panel, na kterém můžete vybrat skupinu dostupnosti.
4. Výběr skupiny dostupnosti služby AD FS

![Konfigurace back-endového fondu interního nástroje pro vyrovnávání zatížení](./media/active-directory-aadconnect-azure-adfs/ilbdeployment3.png)

**6.3. Konfigurace testu**

Na panelu nastavení interního nástroje pro vyrovnávání zatížení vyberte Testy.

1. Klikněte na Přidat.
2. Zadejte podrobnosti testu. a. **Název**: Název testu. b. **Protokol**: TCP. c. **Port**: 443 (HTTPS). d. **Interval**: 5 (výchozí hodnota) – jedná se o interval, ve kterém bude interní nástroj pro vyrovnávání zatížení testovat počítače v back-endovém fondu. e. **Chybný limit prahové hodnoty**: 2 (výchozí hodnota) – jedná se o prahovou hodnotu po sobě jdoucích selhání testu, po kterých bude interní nástroj pro vyrovnávání zatížení deklarovat počítač v back-endovém fondu jako nereagující a přestane na něj směrovat provoz.

![Konfigurace testu interního nástroje pro vyrovnávání zatížení](./media/active-directory-aadconnect-azure-adfs/ilbdeployment4.png)

**6.4. Vytvoření pravidel vyrovnávání zatížení**

Kvůli efektivnímu vyrovnání provozu je nutné nakonfigurovat nástroj pro vyrovnávání zátěže pomocí pravidel vyrovnávání zatížení. Pokud chcete vytvořit pravidlo vyrovnávání zatížení, postupujte následovně: 

1. na panelu nastavení interního nástroje pro vyrovnávání zatížení vyberte pravidlo vyrovnávání zatížení,
2. na panelu pravidla vyrovnávání zatížení klikněte na Přidat.
3. Na panelu pro přidání pravidla vyrovnávání zatížení proveďte následující: a. **Název**: Zadejte název pravidla. b. **Protokol**: Vyberte TCP. c. **Port**: 443. d. **Back-endový port**: 443. e. **Back-endový fond**: Vyberte fond, který jste už dříve vytvořili pro cluster služby AD FS. f. **Test**: Vyberte test, který jste už dříve vytvořili pro servery služby AD FS.

![Konfigurace pravidel interního nástroje pro vyrovnávání zatížení](./media/active-directory-aadconnect-azure-adfs/ilbdeployment5.png)

**6.5. Aktualizace DNS pomocí interního nástroje pro vyrovnávání zatížení**

Přejděte na server DNS a vytvořte záznam CNAME pro interní nástroj pro vyrovnávání zatížení. Záznam CNAME musí pro službu FS obsahovat IP adresu, která odkazuje na IP adresu interního nástroje pro vyrovnávání zatížení. Pokud má interní nástroj pro vyrovnávání zatížení vyhrazenou IP adresu 10.3.0.8 a služba FS je nainstalovaná na webu fs.contoso.com, potom pro fs.contoso.com vytvořte záznam CNAME, který odkazuje na adresu 10.3.0.8.
Tím zajistíte, že se veškerá komunikace (která se týká fs.contoso.com) dostane do interního nástroje pro vyrovnávání zatížení a bude odpovídajícím způsobem směrovaná.

### <a name="7-configuring-the-web-application-proxy-server"></a>7. Konfigurace proxy serverů webových aplikací
**7.1. Konfigurace proxy serverů webových aplikací, aby se mohly spojit se servery služby AD FS**

Aby se proxy servery webových aplikací mohly spojit se servery služby AD FS za interním nástrojem pro vyrovnávání zatížení, vytvořte pro nástroj záznam %systemroot%\system32\drivers\etc\hosts. Všimněte si, že rozlišující název (DN) musí být názvem služby FS, například fs.contoso.com. A IP adresa musí odpovídat IP adrese interního nástroje pro vyrovnávání zatížení (10.3.0.8 – jako v příkladu).

**7.2. Instalace role proxy webových aplikací**

Jakmile si budete jistí, že se proxy servery webových aplikací můžou spojit se servery služby AD FS za interním nástrojem pro vyrovnávání zatížení, můžete v dalším kroku nainstalovat proxy servery webových aplikací. Proxy servery webových aplikací nemusí být připojené k doméně. Výběrem role vzdáleného přístupu nainstalujte role proxy webových aplikací na dva proxy servery webových aplikací. Správce serveru vás provede až do konce instalace WAP.
Další informace o nasazování WAPu najdete v článku [Instalace a konfigurace proxy serveru webových aplikací](https://technet.microsoft.com/library/dn383662.aspx).

### <a name="8--deploying-the-internet-facing-public-load-balancer"></a>8.  Nasazení internetového (veřejného) nástroje pro vyrovnávání zatížení
**8.1.  Vytvoření internetového (veřejného) nástroje pro vyrovnávání zatížení**

Na portálu Azure vyberte Nástroje pro vyrovnávání zatížení a potom klikněte na Přidat. Na panelu Vytvoření nástroje pro vyrovnávání zatížení zadejte následující informace:

1. **Název**: Název nástroje pro vyrovnávání zatížení.
2. **Schéma**: Veřejné – tato možnost informuje Azure, že nástroj pro vyrovnávání zatížení bude potřebovat veřejnou adresu.
3. **IP adresa**: Vytvořte novou IP adresu (dynamickou).

![Internetový nástroj pro vyrovnávání zatížení](./media/active-directory-aadconnect-azure-adfs/elbdeployment1.png)

Po nasazení se nástroj pro vyrovnávání zatížení zobrazí v seznamu nástrojů pro vyrovnávání zatížení.

![Seznam nástrojů pro vyrovnávání zatížení](./media/active-directory-aadconnect-azure-adfs/elbdeployment2.png)

**8.2. Přiřazení názvu DNS k veřejné IP adrese**

Na panelu nástrojů pro vyrovnávání zatížení klikněte na nově vytvořenou položku nástroje pro vyrovnávání zatížení a vyvolejte konfigurační panel. Podle následujících kroků nakonfigurujte název DNS pro veřejnou IP adresu:

1. Klikněte na veřejnou IP adresu. Tím otevřete panel veřejné IP adresy a její nastavení.
2. Klikněte na Konfigurace.
3. Zadejte název DNS. Ten se stane veřejným názvem DNS, ke kterému budete mít přístup odkudkoli, například contosofs.westus.cloudapp.azure.com. Do externí DNS můžete přidat záznam pro službu FS (například fs.contoso.com), který se přeloží do názvu DNS externího nástroje pro vyrovnávání zatížení (contosofs.westus.cloudapp.azure.com).

![Konfigurace internetového nástroje pro vyrovnávání zatížení](./media/active-directory-aadconnect-azure-adfs/elbdeployment3.png) 

![Konfigurace internetového nástroje pro vyrovnávání zatížení (DNS)](./media/active-directory-aadconnect-azure-adfs/elbdeployment4.png)

**8.3. Konfigurace back-endového fondu internetového (veřejného) nástroje pro vyrovnávání zatížení** 

Postupujte stejným způsobem jako při vytváření interního nástroje pro vyrovnávání zatížení a nakonfigurujte back-endový fond internetového (veřejného) nástroje pro vyrovnávání zatížení jako skupinu dostupnosti pro servery WAP. Například contosowapset.

![Konfigurace back-endového fondu internetového nástroje pro vyrovnávání zatížení](./media/active-directory-aadconnect-azure-adfs/elbdeployment5.png)

**8.4. Konfigurace testu**

Postupujte stejným způsobem jako při konfiguraci interního nástroje pro vyrovnávání zatížení a nakonfigurujte test pro back-endový fond serverů WAP.

![Konfigurace testu internetového nástroje pro vyrovnávání zatížení](./media/active-directory-aadconnect-azure-adfs/elbdeployment6.png)

**8.5. Vytvoření pravidel vyrovnávání zatížení**

Postupujte stejným způsobem jako v interním nástroji pro vyrovnávání zatížení a nakonfigurujte pravidlo vyrovnávání zatížení pro protokol TCP 443.

![Konfigurace pravidel vyrovnávání pro internetový nástroj pro vyrovnávání zatížení](./media/active-directory-aadconnect-azure-adfs/elbdeployment7.png)

### <a name="9-securing-the-network"></a>9. Zabezpečení sítě
**9.1. Zabezpečení interní podsítě**

Celkově budete k efektivnímu zabezpečení interní podsítě (v pořadí, jak je uvedeno níže) potřebovat následující pravidla:

| Pravidlo | Popis | Tok |
|:--- |:--- |:---:|
| AllowHTTPSFromDMZ |Povolení komunikace z DMZ pomocí protokolu HTTPS |Příchozí |
| DenyInternetOutbound |Bez přístupu k internetu |Odchozí |

![pravidla přístupu INT (příchozí)](./media/active-directory-aadconnect-azure-adfs/nsg_int.png)

<!--
[comment]: <> (![INT access rules (inbound)](./media/active-directory-aadconnect-azure-adfs/nsgintinbound.png))
[comment]: <> (![INT access rules (outbound)](./media/active-directory-aadconnect-azure-adfs/nsgintoutbound.png))
-->

**9.2. Zabezpečení podsítě DMZ**

| Pravidlo | Popis | Tok |
|:--- |:--- |:---:|
| AllowHTTPSFromInternet |Povolení komunikace pomocí protokolu HTTPS z internetu do podsítě DMZ |Příchozí |
| DenyInternetOutbound |Všechno kromě komunikace pomocí protokolu HTTPS do internetu je blokováno. |Odchozí |

![pravidla přístupu EXT (příchozí)](./media/active-directory-aadconnect-azure-adfs/nsg_dmz.png)

<!--
[comment]: <> (![EXT access rules (inbound)](./media/active-directory-aadconnect-azure-adfs/nsgdmzinbound.png))
[comment]: <> (![EXT access rules (outbound)](./media/active-directory-aadconnect-azure-adfs/nsgdmzoutbound.png))
-->

> [!NOTE]
> Pokud je požadováno ověření klienta uživatelským certifikátem (ověřování klienta protokolem TLS pomocí uživatelských certifikátů X509), potom služba AD FS vyžaduje, aby port 49443 TCP měl povolený příchozí přístup.
> 
> 

### <a name="10-test-the-ad-fs-sign-in"></a>10. Test přihlášení ke službě AD FS
Nejjednodušší způsob otestování služby AD FS je pomocí stránky IdpInitiatedSignon.aspx. Abyste to mohli provést, musíte ve vlastnostech služby AD FS povolit IdpInitiatedSignOn. Pomocí níže uvedených pokynů ověřte nastavení služby AD FS.

1. Pomocí PowerShellu spusťte níže uvedenou rutinu na serveru služby AD FS a povolte požadovanou možnost.
   Set-AdfsProperties -EnableIdPInitiatedSignonPage $true 
2. Z libovolného přístupu pomocí externího počítače https://adfs.thecloudadvocate.com/adfs/ls/IdpInitiatedSignon.aspx  
3. Měla by se zobrazit stránka služby AD FS podobná níže uvedenému příkladu:

![Přihlašovací stránka testu](./media/active-directory-aadconnect-azure-adfs/test1.png)

Po úspěšném přihlášení zobrazí zprávu o úspěchu, jak je uvedeno níže:

![Úspěch testu](./media/active-directory-aadconnect-azure-adfs/test2.png)

## <a name="template-for-deploying-ad-fs-in-azure"></a>Šablona pro nasazení AD FS v Azure
Šablona nasadí nastavení pro šest počítačů, po dvou pro každý řadič domény, službu AD FS a protokol WAP.

[Šablona pro nasazení AD FS v Azure](https://github.com/paulomarquesc/adfs-6vms-regular-template-based)

Při nasazování této šablony můžete použít stávající virtuální síť nebo vytvořit novou. Níže najdete různé parametry, pomocí nichž si můžete přizpůsobit nasazení, a popis jejich využití v procesu nasazení. 

| Parametr | Popis |
|:--- |:--- |
| Umístění |Oblast, do které se mají nasadit prostředky, například Východní USA. |
| StorageAccountType |Typ vytvořeného účtu úložiště |
| VirtualNetworkUsage |Označuje, zda se vytvoří nová virtuální síť nebo se použije již existující. |
| VirtualNetworkName |Název virtuální sítě, která se má vytvořit. Tento parametr je povinný bez ohledu na to, zda použijete existující virtuální síť nebo vytvoříte novou. |
| VirtualNetworkResourceGroupName |Určuje název skupiny prostředků, ve které se nachází existující virtuální síť. Při použití existující virtuální sítě je tento parametr povinný, aby nasazení našlo ID existující virtuální sítě. |
| VirtualNetworkAddressRange |Rozsah adres nové virtuální sítě. Parametr je povinný při vytváření nové virtuální sítě. |
| InternalSubnetName |Název interní podsítě. Tento parametr je povinný pro obě možnosti použití virtuální sítě (nové nebo existující). |
| InternalSubnetAddressRange |Rozsah adres interní podsítě, která obsahuje řadiče domény a servery ADFS. Parametr je povinný při vytváření nové virtuální sítě. |
| DMZSubnetAddressRange |Rozsah adres podsítě DMZ, která obsahuje proxy servery aplikací pro Windows. Parametr je povinný při vytváření nové virtuální sítě. |
| DMZSubnetName |Název interní podsítě. Tento parametr je povinný pro obě možnosti použití virtuální sítě (nové nebo existující). |
| ADDC01NICIPAddress |Interní IP adresa prvního řadiče domény. Tato IP adresa se staticky přiřadí řadiči domény. Musí se jednat o platnou IP adresu v rámci interní podsítě. |
| ADDC02NICIPAddress |Interní IP adresa druhého řadiče domény. Tato IP adresa se staticky přiřadí řadiči domény. Musí se jednat o platnou IP adresu v rámci interní podsítě. |
| ADFS01NICIPAddress |Interní IP adresa prvního serveru ADFS. Tato IP adresa se staticky přiřadí serveru ADFS. Musí se jednat o platnou IP adresu v rámci interní podsítě. |
| ADFS02NICIPAddress |Interní IP adresa druhého serveru ADFS. Tato IP adresa se staticky přiřadí serveru ADFS. Musí se jednat o platnou IP adresu v rámci interní podsítě. |
| WAP01NICIPAddress |Interní IP adresa prvního serveru WAP. Tato IP adresa se staticky přiřadí serveru WAP. Musí se jednat o platnou IP adresu v rámci podsítě DMZ. |
| WAP02NICIPAddress |Interní IP adresa druhého serveru WAP. Tato IP adresa se staticky přiřadí serveru WAP. Musí se jednat o platnou IP adresu v rámci podsítě DMZ. |
| ADFSLoadBalancerPrivateIPAddress |Interní IP adresa nástroje pro vyrovnávání zatížení ADFS. Tato IP adresa se staticky přiřadí nástroji pro vyrovnávání zatížení. Musí se jednat o platnou IP adresu v rámci interní podsítě. |
| ADDCVMNamePrefix |Prefix názvu virtuálního počítače pro řadiče domény |
| ADFSVMNamePrefix |Prefix názvu virtuálního počítače pro servery ADFS |
| WAPVMNamePrefix |Prefix názvu virtuálního počítače pro servery WAP |
| ADDCVMSize |Velikost virtuálního počítače řadičů domény |
| ADFSVMSize |Velikost virtuálního počítače serverů ADFS |
| WAPVMSize |Velikost virtuálního počítače serverů WAP |
| AdminUserName |Jméno místního správce virtuálních počítačů |
| AdminPassword |Heslo k účtu místního správce virtuálních počítačů |

## <a name="additional-resources"></a>Další zdroje
* [Skupiny dostupnosti](https://aka.ms/Azure/Availability) 
* [Nástroj pro vyrovnávání zatížení Azure](https://aka.ms/Azure/ILB)
* [Interní nástroj pro vyrovnávání zatížení.](https://aka.ms/Azure/ILB/Internal)
* [Internetový nástroj pro vyrovnávání zatížení](https://aka.ms/Azure/ILB/Internet)
* [Účty úložiště](https://aka.ms/Azure/Storage)
* [Virtuální sítě Azure](https://aka.ms/Azure/VNet)
* [Služba AD FS a odkazy na proxy webové aplikace](https://aka.ms/ADFSLinks) 

## <a name="next-steps"></a>Další kroky
* [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md)
* [Konfigurace a správa služby AD FS pomocí služby Azure AD Connect](active-directory-aadconnectfed-whatis.md)
* [Vysoká dostupnost mezi geografickými nasazeními služby AD FS v Azure pomocí Azure Traffic Manageru](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)

