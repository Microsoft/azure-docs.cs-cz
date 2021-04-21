---
title: Azure AD Secure Hybrid Access s průvodcem nasazením F5 | Microsoft Docs
description: Kurz k nasazení virtuálního počítače F5 BIG-IP Virtual Edition (VE verzi) ve službě Azure IaaS pro zajištění zabezpečeného hybridního přístupu
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 139009c55573e1e115a22069671f66e93695a635
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783318"
---
# <a name="tutorial-to-deploy-f5-big-ip-virtual-edition-vm-in-azure-iaas-for-secure-hybrid-access"></a>Kurz nasazení virtuálního počítače F5 BIG-IP Virtual Edition ve službě Azure IaaS pro zajištění zabezpečeného hybridního přístupu

Tento kurz vás provede koncovým procesem nasazení verze BIG-IP vitural Edition (VE verzi) v Azure IaaS. Na konci tohoto kurzu byste měli mít následující:

- Plně připravený virtuální počítač pro velký počet IP adres (VM) pro modelování ověření konceptu zabezpečeného hybridního přístupu (SHA)

- Pracovní instance, která se má použít k testování nových aktualizací systému BIG-IP a oprav hotfix

## <a name="prerequisites"></a>Požadavky

Předchozí prostředí BIG-IP s velkým objemem adres nebo znalostí se ale nedoporučuje, ale doporučujeme, abyste familiarizingi pomocí [terminologie F5 Big-IP](https://www.f5.com/services/resources/glossary). Nasazení služby BIG-IP v Azure pro službu SHA vyžaduje:

- Placené předplatné Azure nebo bezplatný [zkušební předplatné](https://azure.microsoft.com/free/)na 12 měsíců.

- Kterákoli z následujících SKU licence na verzi F5 BIG-IP

  - ® Nejlepší sada pro velkou IP adresu F5

  - F5 – samostatná licence pro správce zásad pro BIG-IP Access™ (APM)

  - F5™ licence pro přístup správce zásad pro Big-IP Access Manager (APM) na stávající® místní Traffic Manager™ (LTM)
  
  - 90 denní [zkušební licence](https://www.f5.com/trial/big-ip-trial.php)pro Big-IP s plnou funkcí.

- Certifikát se zástupným znakem nebo alternativním názvem subjektu (SAN) pro publikování webových aplikací přes protokol SSL (Secure Socket Layer). [Zašifrujeme si](https://letsencrypt.org/) bezplatný 90 dní pro testování.

- Certifikát SSL pro zabezpečení rozhraní správy BIG-IPs. Certifikát používaný k publikování webových aplikací se dá použít, pokud jeho subjekt odpovídá plně kvalifikovanému názvu domény (FQDN) BIG-IP. Například certifikát se zástupným znakem definovaný subjektem *. contoso.com by byl vhodný pro `https://big-ip-vm.contoso.com:8443`

Nasazení virtuálních počítačů a konfigurace základních systémů budou trvat přibližně 30 minut, přičemž vaše platforma pro velké IP adresy bude připravená na implementaci libovolného ze scénářů SHA, které jsou [tady](f5-aad-integration.md)uvedené.

V tomto kurzu se předpokládá, že se pro testování scénářů nasadí Velká IP adresa do skupiny prostředků Azure obsahující prostředí Active Directory (AD). Prostředí by se mělo skládat z virtuálního počítače řadiče domény (DC) a webového hostitele (IIS). Díky tomu, že se tyto servery nacházejí v jiných umístěních k virtuálnímu počítači s ROZSÁHLÝmi IP adresami, je v pořádku také OK, což poskytuje velkou IP adresu pro každou roli potřebnou k podpoře daného scénáře. Podporovány jsou i scénáře, kdy je virtuální počítač se systémem BIG-IP připojený k jinému prostředí prostřednictvím připojení VPN.

Pokud tady nemáte uvedené položky k testování, můžete pomocí tohoto [skriptu](https://github.com/Rainier-MSFT/Cloud_Identity_Lab)nasadit do Azure celé prostředí domény služby AD. Kolekci ukázkových testovacích aplikací můžete také programově nasadit na webového hostitele služby IIS pomocí tohoto [skriptované automatizace](https://github.com/jeevanbisht/DemoSuite).

>[!NOTE]
>[Azure Portal](https://portal.azure.com/#home) se neustále vyvíjí, takže některé kroky v tomto kurzu se mohou lišit od skutečného rozložení zjištěného v Azure Portal.

## <a name="azure-deployment"></a>Nasazení Azure

ROZSÁHLá IP adresa se dá nasadit v různých topologiích. Tato příručka se zaměřuje na nasazení jediného síťového rozhraní (NIC). Pokud však nasazení BIG-IP vyžaduje více síťových rozhraní pro vysokou dostupnost, oddělení sítě nebo více než 1 GB propustnosti, zvažte použití šablon F5's předem kompilovaných [Azure Resource Manager (ARM)](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_multiNIC.html).

Provedením následujících kroků nasaďte ve službě BIG-IP z [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps).

1. Přihlaste se k [Azure Portal](https://portal.azure.com/#home) pomocí účtu, který má oprávnění k vytváření virtuálních počítačů. Například Přispěvatel

2. Do horního vyhledávacího pole pásu karet zadejte **Marketplace** a potom **Zadejte**

3. Do filtru Marketplace zadejte **F5** následovaný **ENTER**

4. Na horním pásu karet vyberte **+ Přidat** a do filtru Marketplace zadejte **F5** a potom **zadejte Enter** .

5. Vyberte **F5 Big-IP Virtual Edition (BYOL)**  >  **vybrat softwarový plán**  >  **F5 Big-IP ve všech (BYOL, 2 spouštěcí místa)**

6. Vyberte **Vytvořit**.

![Obrázek ukazuje kroky pro výběr plánu softwaru.](./media/f5ve-deployment-plan/software-plan.png)

7. Projděte si nabídku **základní** a použijte následující nastavení.

 |  Podrobnosti o projektu     |  Hodnota     |
 |:-------|:--------|
 |Předplatné|Cílové předplatné pro nasazení virtuálního počítače s velkou IP adresou|
 |Skupina prostředků | Existující skupina prostředků Azure: virtuální počítač BIG-IP se nasadí do služby nebo ho vytvoří. Musí se jednat o stejnou skupinu prostředků pro virtuální počítače DC a IIS.|
 | **Podrobnosti o instancích**|  |
 |Název virtuálního počítače| Příklad BIG-IP-VM |
 |Oblast | Cílová geografická platforma Azure pro virtuální počítače s velkým objemem IP adres |
 |Možnosti dostupnosti| Povolit jenom při použití virtuálního počítače v produkčním prostředí|
 |Image| BIG-IP. F5 – vše (BYOL, 2 spouštěcí umístění)|
 |Instance Azure Spot| Ne, ale pokud je to vhodné, můžete to povolit |
 |Velikost| Minimální specifikace by měla být 2 vCPU a 8 GB paměti.|
 |**Účet správce**|  |
 |Typ ověřování|Vyberte heslo pro nyní. Můžete přepnout na pár klíčů později. |
 |Uživatelské jméno|Identita, která se vytvoří jako místní účet BIG-IP pro přístup k rozhraním pro správu. Uživatelské jméno rozlišuje velká a malá písmena.|
 |Heslo|Zabezpečený přístup správce se silným heslem|
 |**Pravidla portů pro příchozí spojení**|  |
 |Veřejné příchozí porty|Žádná|

8. Vyberte **Další: disky** ponechte všechny výchozí hodnoty a vyberte **Další: sítě**.

9. V nabídce **sítě** dokončete tato nastavení.

 |Síťové rozhraní|      Hodnota |
 |:--------------|:----------------|
 |Virtuální síť|Stejnou virtuální síť Azure, kterou používá vaše virtuální počítače DC a IIS, nebo si ji vytvořte|
 |Podsíť| Stejná interní podsíť Azure jako virtuální počítače DC a IIS, nebo si ji vytvořte|
 |Veřejná IP adresa |  Žádná|
 |Skupina zabezpečení sítě NIC| Pokud je podsíť Azure, kterou jste vybrali v předchozích krocích, už přidružená ke skupině zabezpečení sítě (NSG), vyberte možnost žádná. jinak vyberte základní.|
 |Zrychlení sítě| Vypnout |
 |**Vyrovnávání zatížení**|     |
 |Virtuální počítač pro vyrovnávání zatížení| No|

10. Vyberte **Další: Správa** a dokončete tato nastavení.

 |Monitorování|    Hodnota |
 |:---------|:-----|
 |Podrobné sledování| Vypnout|
 |Diagnostika spouštění|Povolte s vlastním účtem úložiště. Pomocí možnosti sériového prostředí v Azure Portal umožňuje připojení k rozhraní Secure Shell pro velké IP adresy (SSH). Vyberte libovolný dostupný účet Azure Storage.|
 |**Identita**|  |
 |Spravovaná identita přiřazená systémem|Vypnout|
 |Azure Active Directory|Služba BIG-IP tuto možnost v tuto chvíli nepodporuje.|
 |**Automatické vypnutí**|    |
 |Povolit automatické vypnutí| Při testování zvažte možnost nastavování sítě BIG-IP-VM na denní vypnutí.|

11. Vyberte **Další: Upřesnit** zachová všechny výchozí hodnoty a vyberte **Další: značky**.

12. Vyberte **Další: zkontrolovat + vytvořit** pro kontrolu konfigurace Big-IP-VM před výběrem možnosti **vytvořit** , aby se nasazení zahájilo.

13. Čas k úplnému nasazení virtuálního počítače s velkým objemem IP adres obvykle je 5 minut. Pokud jste hotovi, nevybírejte možnost **Přejít k prostředku**, místo toho rozbalení nabídky na levé straně Azure Portal a vybráním **skupin prostředků** přejděte k novému VIRTUÁLNÍMU počítači s velkou IP adresou. Pokud se virtuální počítač nepovede vytvořit, vyberte **zpět** a **Další**.

## <a name="network-configuration"></a>Konfigurace sítě

Při prvním spuštění virtuálního počítače s velkou IP adresou se jeho síťová karta zřídí s **primární** privátní IP adresou, kterou vystavila služba DHCP (Dynamic Host Configuration Protocol) v podsíti Azure, ke které je připojený. Tuto IP adresu bude používat operační systém správy provozu ve velkém protokolu IP (TMOS) ke komunikaci s těmito službami:

- Komunikace s ostatními hostiteli a službami

- Odchozí přístup k veřejnému Internetu

- Příchozí přístup k rozhraním pro správu BIG-IPs web config a SSH

Vystavení kterékoli z těchto rozhraní pro správu na internetu zvyšuje BIG-IPsý povrch pro útoky, proto, proč během nasazování nebyla zajištěna BIG-IPs primární IP adresa s veřejnou IP adresou. Místo toho se pro publikování služby zřídí sekundární interní IP adresa a přidružená veřejná IP adresa.
Toto 1 až 1 mapování mezi veřejnou IP adresou virtuálního počítače a privátní IP adresou umožňuje externí provoz, aby se mohl připojit k virtuálnímu počítači. Pravidlo Azure NSG je ale také potřeba k tomu, aby umožňovalo provoz, a to podobně jako brána firewall.

Diagram znázorňuje nasazení s jedním síťovým ADAPTÉRem ve službě Azure v Azure, které je nakonfigurované s primární IP adresou pro obecné operace a správu, a pro služby publikování oddělené IP adresy virtuálních serverů.
V tomto uspořádání pravidlo NSG umožňuje vzdálenému provozu určenému pro `intranet.contoso.com` směrování na veřejnou IP adresu pro publikovanou službu, a to před tím, než se přepošle na virtuální server Big-IP.

![Obrázek ukazuje nasazení jediné síťové karty ](./media/f5ve-deployment-plan/single-nic-deployment.png) ve výchozím nastavení jsou privátní a veřejné IP adresy vystavené virtuálním počítačům Azure vždycky dynamické, takže se při každém restartování virtuálního počítače nejspíš změní. Vyhněte se nepředvídatelným problémům s připojením změnou IP adresy pro správu BIG-IPs na statickou a proveďte stejné sekundární IP adresy, které se používají pro publikování služeb.

1. V nabídce virtuálního počítače s velkým objemem IP adres přejděte na **Nastavení**  >  **sítě** .

2. V zobrazení síť vyberte odkaz napravo od **síťového rozhraní** .

![Obrázek ukazuje konfiguraci sítě.](./media/f5ve-deployment-plan/network-config.png)

>[!NOTE]
>Názvy virtuálních počítačů se při nasazení vygenerují náhodně.

3. V levém podokně vyberte **Konfigurace protokolu IP** a pak vyberte **ipconfig1** řádek.

4. Nastavte možnost **přiřazování IP** adres na statickou a v případě potřeby změňte primární IP adresu virtuálních počítačů se stejnou IP adresou. Pak vyberte **Uložit** a zavřete nabídku ipconfig1

>[!NOTE]
>Tuto primární IP adresu použijete pro připojení a správu virtuálního počítače s velkou IP adresou.

5. Na horním pásu karet vyberte **+ Přidat** a zadejte název sekundární privátní IP adresy, například ipconfig2.

6. Nastavte možnost **přidělení** nastavení privátní IP adresa na **static**. Poskytnutí nejbližšího nebo nižšího po sobě jdoucích IP adres pomáhá udržet věci.

7. Nastavte veřejnou IP adresu, kterou chcete **přidružit** , a vyberte **vytvořit** .

8. Zadejte název nové veřejné IP adresy, například BIG-IP-VM_ipconfig2_Public.

9. Pokud se zobrazí výzva, nastavte **SKU** na standard.

10. Pokud se zobrazí výzva, nastavte **vrstvu** na **globální** a

11. Nastavte možnost **přiřazení** na **statickou** a pak dvakrát vyberte **OK** .

Váš virtuální počítač s velkým objemem IP adres je teď připravený k nastavení pomocí:

- **Primární privátní IP adresa**: vyhrazená pro správu cloudu Big-IP-VM prostřednictvím jeho nástroje Web config a SSH. Použije ho systém BIG-IP jako jeho **vlastní IP adresu** pro připojení k publikovaným back-end službám. Také se připojuje k externím službám, jako jsou NTP, AD a LDAP.

- **Sekundární privátní IP adresa**: používá se při vytváření virtuálního serveru s využitím služby Big IP APM k naslouchání příchozímu požadavku publikovaným službám.

- **Veřejná IP adresa**: přidružená k sekundární privátní IP adrese, umožňuje klientským provozům z veřejného Internetu získat přístup k virtuálnímu serveru s velkou IP adresou pro publikované služby.

Tento příklad znázorňuje vztah 1:1 mezi veřejnými a soukromými IP adresami virtuálního počítače. Síťová karta virtuálního počítače Azure může mít jenom jednu primární IP adresu a všechny další vytvořené IP adresy se vždycky označují jako sekundární.

>[!NOTE]
>K publikování služeb BIG-IP budete potřebovat sekundární mapování IP adres.

![Tento obrázek ukazuje všechny sekundární IP adresy.](./media/f5ve-deployment-plan/secondary-ips.png)

K implementaci služby SHA pomocí **aplikace s asistencí pro přístup** s vysokým IP adresou zopakujte kroky 5-11 pro vytvoření dalších privátních a veřejných párů IP adres pro každou další službu, kterou plánujete publikovat prostřednictvím funkce APM pro velkou IP adresu. Stejný přístup se dá použít i v případě publikování služeb pomocí BIG-IPs **Pokročilé konfigurace**. V takovém scénáři však máte možnost zabránit přeměně veřejných IP adres pomocí konfigurace [indikátoru názvu serveru (SNI)](https://support.f5.com/csp/#/article/K13452) . V této konfiguraci bude virtuální server s velkým protokolem IP přijímat všechny přenosy klientů, které obdrží, a směrovat ho do svého cíle.

## <a name="dns-configuration"></a>Konfigurace DNS

Aby klienti mohli překládat vaše publikované služby SHA na veřejné IP adresy virtuálních počítačů s velkými IP adresami, je nutné, aby byly nakonfigurovány na serveru DNS.

Následující postup předpokládá, že zóna DNS veřejné domény používané pro vaše služby SHA je spravovaná v Azure. Nicméně stejné zásady DNS při vytváření záznamu lokátoru pořád platí bez ohledu na to, kde je vaše zóna DNS spravovaná.

1. Pokud ještě není otevřený, rozbalte nabídku na levé straně portálu a přejděte k VIRTUÁLNÍmu počítači s velkou IP adresou přes možnost **skupiny prostředků** .

2. V nabídce virtuálního počítače s velkým objemem IP adres přejděte na **Nastavení**  >  **sítě** .

3. V zobrazení síť BIG-IP-VM sítě vyberte první sekundární IP adresu v rozevíracím seznamu konfigurace IP adresy a vyberte odkaz pro **veřejnou IP adresu síťové karty** .

![Snímek obrazovky zobrazující veřejnou IP adresu síťové karty](./media/f5ve-deployment-plan/networking.png)

4. V levém podokně v části **Nastavení** vyberte **Konfigurace** a zobrazte nabídku veřejné IP adresy a vlastnosti DNS pro vybranou sekundární IP adresu.

5. Vyberte a **vytvořte** záznam aliasu a v rozevíracím seznamu vyberte svoji **zónu DNS** . Pokud zóna DNS ještě neexistuje, může být spravovaná mimo Azure, nebo můžete vytvořit jednu pro příponu domény, kterou jste ověřili v Azure AD.

6. K vytvoření prvního záznamu aliasu DNS použijte následující podrobnosti:

 | Pole | Hodnota |
 |:-------|:-----------|
 |Předplatné| Stejné předplatné jako virtuální počítač BIG-IP-VM|
 |Zóna DNS| Zóna DNS, která je autoritativní pro ověřenou příponu domény, kterou budou používat vaše publikované weby, například www.contoso.com |
 |Name | Název hostitele, který zadáte, se přeloží na veřejnou IP adresu, která je přidružená k vybrané sekundární IP adrese. Ujistěte se, že jste definovali správné mapování DNS na IP adresu. Viz část poslední obrázek v části Konfigurace sítě, například intranet.contoso.com > 13.77.148.215|
 | TTL | 1 |
 |Jednotky TTL | Hodiny |

7. Vyberte **vytvořit** pro Azure a uložte tato nastavení do veřejné služby DNS.

8. Ponechte **popisek název DNS (volitelné)** a před zavřením nabídky veřejné IP adresy vyberte **Uložit** .

9. Opakujte kroky 1 až 6 pro vytvoření dalších záznamů DNS pro každou službu, kterou plánujete publikovat pomocí Průvodce konfigurací s asistencí pro velkou IP adresu.

  Pomocí záznamů DNS můžete použít libovolný z online nástrojů, jako je například [Kontrola DNS](https://dnschecker.org/) , a ověřit tak, že byl vytvořený záznam úspěšně šířený napříč všemi globálními veřejnými servery DNS.

  Pokud spravujete obor názvů domény DNS pomocí externího poskytovatele, jako je [GoDaddy](https://www.godaddy.com/), pak budete muset vytvořit záznamy pomocí vlastního zařízení pro správu DNS.

>[!NOTE]
>V případě testování a často přepínání záznamů DNS můžete použít také místní soubor hostitelů počítače. K souboru localhost na počítači s Windows se dá získat pøístup stisknutím Win + R na klávesnici a odesláním **ovladačů** Wordu do pole Spustit. Stačí mít na vědomí, že záznam localhost bude poskytovat jenom překlad DNS pro místní počítač, ne pro ostatní klienty.

## <a name="client-traffic"></a>Provoz klienta

Ve výchozím nastavení jsou Azure virtuální sítě a přidružené podsítě privátní sítě, které nemůžou přijímat internetový provoz. Síťový adaptér BIG-IP-VM by měl být připojený k NSG, který jste zadali během nasazování. Pro externí webový provoz, který se má připojit k virtuálnímu počítači BIG-IP-VM, je nutné definovat příchozí pravidlo NSG, které povoluje porty 443 (HTTPS) a 80 (HTTP) přes veřejný Internet.

1. V **hlavní nabídce** virtuálního počítače s velkými IP adresami vyberte **sítě** .

2. Vyberte **Přidat** příchozí pravidlo a zadejte následující vlastnosti pravidla NSG:

 |     Pole   |   Hodnota        |
 |:------------|:------------|
 |Zdroj| Libovolný|
 |Rozsahy zdrojových portů| *|
 |Cílové IP adresy|Čárkami oddělený seznam všech privátních privátních IP adres typu BIG-IP-VM|
 |Cílové porty| 80,443|
 |Protokol| TCP |
 |Akce| Povolit|
 |Priorita|Nejnižší dostupná hodnota mezi 100 – 4096|
 |Name | Popisný název, například: `BIG-IP-VM_Web_Services_80_443`|

3. Vyberte **Přidat** a potvrďte změny a zavřete nabídku **sítě** .

Přenosy HTTP a HTTPS z libovolného umístění teď budou mít povolený přístup ke všem sekundárním rozhraním vašich virtuálních počítačů s velkou IP adresou. Povolení portu 80 je užitečné v případě, že funkce APM pro velké IP adresy automaticky přesměruje uživatele z HTTP na HTTPS. Toto pravidlo se dá upravit tak, aby se v případě potřeby přidaly nebo odebraly cílové IP adresy.

## <a name="manage-big-ip"></a>Správa BIG-IP

Systém BIG-IP je spravován prostřednictvím jeho uživatelského rozhraní pro konfiguraci webu, ke kterému se dá dostat pomocí kterékoli z následujících doporučených metod:

- Z počítače v interní síti BIG-IP

- Z klienta VPN připojeného k interní síti BIG-IP-VM

- Publikováno prostřednictvím [Azure proxy aplikací služby AD](./application-proxy-add-on-premises-application.md)

Abyste mohli pokračovat ve zbývajících konfiguracích, budete se muset rozhodnout na nejvhodnější metodu. V případě potřeby se můžete přímo připojit ke konfiguraci webu z Internetu, a to tak, že nakonfigurujete primární IP adresu BIG-IP s veřejnou IP adresou. Pak přidejte pravidlo NSG, které povolí přenos 8443 k této primární IP adrese. Nezapomeňte omezit zdroj na vlastní důvěryhodnou IP adresu, jinak se bude moci připojit kdokoli.

Až budete připraveni, potvrďte, že se můžete připojit k webové konfiguraci virtuálního počítače BIG-IP a přihlásit se pomocí přihlašovacích údajů zadaných během nasazování virtuálního počítače:

- Pokud se připojujete z virtuálního počítače v jeho interní síti nebo přes VPN, připojte se přímo k BIG-IPs primární IP adresa a port konfigurace webu. Například, `https://<BIG-IP-VM_Primary_IP:8443`. V prohlížeči se zobrazí výzva k zadání nezabezpečeného připojení, ale můžete ho ignorovat, dokud nebude nakonfigurovaná Velká IP adresa. Pokud prohlížeč zabrání v blokování přístupu, vymažte jeho mezipaměť a akci opakujte.

- Pokud jste publikovali webovou konfiguraci prostřednictvím proxy aplikace, pak použijte adresu URL definovanou pro přístup k webové konfiguraci externě, bez připojení portu, například `https://big-ip-vm.contoso.com` . Interní adresa URL musí být definována pomocí portu webové konfigurace, například `https://big-ip-vm.contoso.com:8443` 

Systém BIG-IP je možné spravovat taky prostřednictvím svého základního prostředí SSH, které se obvykle používá pro úlohy příkazového řádku (CLI) a přístup na úrovni root. Pro připojení k rozhraní příkazového řádku existuje několik možností, včetně:

- [Služba Azure bastionu](../../bastion/bastion-overview.md): umožňuje rychlé a zabezpečené připojení k LIBOVOLNÉmu virtuálnímu počítači v rámci virtuální sítě, a to z libovolného místa.

- Přímé připojení pomocí klienta SSH, jako je například výstup pomocí postupu JIT

- Sériová konzola: nabízená na konci nabídky podpora a řešení potíží v nabídce virtuálních počítačů na portálu. Nepodporuje přenosy souborů.

- Stejně jako u webové konfigurace se můžete připojit přímo k rozhraní příkazového řádku z Internetu, a to tak, že nakonfigurujete primární IP adresu BIG-IP s veřejnou IP adresou a přidáte pravidlo NSG, které umožní provoz SSH. Pokud tuto metodu použijete, nezapomeňte zdroj omezit na vlastní důvěryhodnou IP adresu.  

## <a name="big-ip-license"></a>Licence na BIG-IP

Aby bylo možné nakonfigurovat služby pro publikování a SHA, musí být systém BIG-IP aktivován a zajištěn modulem APM.

1. Přihlaste se zpátky do webové konfigurace a na stránce **Obecné vlastnosti** vyberte **aktivovat** .

2. V poli **základní registrační klíč** zadejte klíč citlivý na velká a malá písmena, která poskytuje F5.

3. Ponechte **metodu aktivace** nastavenou na hodnotu **automaticky** a vyberte možnost **Další**, služba Big-IP ověří licenci a zobrazí licenční smlouvu s koncovým uživatelem (EULA).

4. Před výběrem možnosti **pokračovat** vyberte **přijmout** a počkejte na dokončení aktivace.

5. Přihlaste se zpátky a v dolní části stránky Souhrn licence vyberte **Další**. V části BIG-IP se teď zobrazí seznam modulů, které poskytují různé funkce, které jsou potřeba pro SHA. Pokud to nevidíte, na hlavní kartě přejděte na zřizování **systémových**  >  **prostředků**.

6. Podívejte se na sloupec zřizování pro zásady přístupu (APM).

![Obrázek ukazuje zřizování přístupu.](./media/f5ve-deployment-plan/access-provisioning.png)

7. Vyberte **Odeslat** a přijmout upozornění.

8. Musí být pacient a čekat na to, až tato velká IP adresa dokončí nové funkce. Může se několikrát cyklicky opakovat, než se plně inicializuje. 

9. Až budete připraveni, vyberte **pokračovat** a na kartě **informace** vyberte **spustit instalační program** .

>[!IMPORTANT]
>Licence F5 jsou omezené tak, aby se v jednom okamžiku využily jednou instancí BIG-IP. Mohou nastat důvody pro migraci licence z jedné instance do druhé, a pokud tak učiníte, nezapomeňte před vyřazením licence na aktivní instanci tuto zkušební licenci [odvolat](https://support.f5.com/csp/article/K41458656) , jinak se licence trvale ztratí.

## <a name="provision-big-ip"></a>Zřídit ROZSÁHLou IP adresu

Zabezpečení provozu správy do a z BIG-IPs webové konfigurace je nejdůležitější. Nakonfigurujte certifikát správy zařízení, aby chránil kanál webové konfigurace před ohrožením.

1. V levém navigačním panelu přejdete do části **System**  >  **Certificate Management**  >  **Traffic** Certificate  >  **list import seznam certifikátů protokol SSL**  >  

2. V rozevíracím seznamu **typ importu** vyberte **PKCS 12 (IIS)** a **Zvolte soubor**. Vyhledejte webový certifikát SSL s názvem subjektu nebo SAN, který bude pokrývat plně kvalifikovaný název domény, který budete přiřazovat k virtuálnímu počítači BIG-IP-VM v několika dalších krocích.

3. Zadejte heslo pro certifikát a pak vyberte **importovat** .

4. V levém navigačním panelu přejít na **systémovou**  >  **platformu**

5. Nakonfigurujte virtuální počítač BIG-IP-VM s plně kvalifikovaným názvem hostitele a časovým pásmem pro jeho prostředí, a potom s **aktualizací** .

![Obrázek zobrazuje obecné vlastnosti.](./media/f5ve-deployment-plan/general-properties.png)

6. V levém navigačním panelu přejít na konfigurace **systému**  >    >  **zařízení**  >  **NTP**

7. Zadejte spolehlivý zdroj NTP a vyberte **Přidat** a pak **aktualizace**. Například `time.windows.com`.

Nyní potřebujete záznam DNS k vyřešení BIG-IPs plně kvalifikovaného názvu domény zadaného v předchozích krocích na jeho primární privátní IP adresu. Do interního DNS vašeho prostředí nebo do souboru localhost počítače, který se bude používat pro připojení k webové konfiguraci BIG-IP, by se měl přidat záznam. V obou případech by se po přímém připojení ke konfiguraci webu neměla zobrazovat upozornění prohlížeče. To znamená, ne prostřednictvím proxy aplikace nebo žádného jiného reverzního proxy serveru.

## <a name="ssl-profile"></a>Profil SSL

Jako reverzní proxy se může systém BIG-IP chovat jako jednoduchá předávací služba, jinak se nazývá transparentní proxy server nebo úplný proxy server, který se aktivně účastní výměn mezi klienty a servery.
Úplný proxy vytvoří dvě odlišná připojení: připojení klienta front-end TCP spolu s odděleným připojením k back-endu TCP serveru, které se propojí za měkké mezery uprostřed. Klienti se připojují k naslouchacímu procesu proxy na jednom konci, jinak se říká **virtuální server** a proxy vytvoří samostatné nezávislé připojení k back-end serveru. To je obousměrné na obou stranách.
V tomto režimu úplného proxy serveru může systém F5 pro kontrolu provozu kontrolovat provoz, a proto je možné pracovat také s požadavky a odpověďmi. Některé funkce, jako je vyrovnávání zatížení a optimalizace výkonu webu, i pokročilejší služby pro správu provozu, jako je zabezpečení vrstvy aplikace, Webová akcelerace, směrování stránek a zabezpečený vzdálený přístup, využívají tuto funkci.
Při publikování služeb založených na protokolu SSL se proces dešifrování a šifrování mezi klienty a back-end službami zpracovává pomocí profilů pro velké IP SSL.

Existují dva typy profilů:

- **SSL klienta**: nejběžnější způsob, jak nastavit systém Big-IP pro publikování interních služeb pomocí protokolu SSL, je vytvoření profilu SSL klienta. S profilem SSL klienta může systém BIG-IP dešifrovat příchozí požadavky klientů před jejich odesláním do služby pro příjem dat. Pak před odesláním do klientů šifruje odpovědi na odchozí back-end.

- **SSL serveru**: u back-end služeb nakonfigurovaných pro protokol HTTPS můžete nakonfigurovat Big-IP, aby používaly profil SSL serveru. Pomocí profilu SSL serveru služba BIG-IP znovu zašifruje požadavek klienta před odesláním do cílové back-end služby. Když server vrátí šifrovanou odpověď, systém BIG-IP dešifruje a znovu zašifruje odpověď před odesláním do klienta prostřednictvím nakonfigurovaného profilu SSL klienta.

Zřizování profilů SSL pro klienty i servery budou mít předem nakonfigurovanou a připravenou službu BIG-IP pro všechny scénáře služby SHA.

1. V levém navigačním panelu přejdete do části **System**  >  **Certificate Management**  >  **Traffic** Certificate  >  **list import seznam certifikátů protokol SSL**  >  

2. V rozevíracím seznamu **typ importu** vyberte **PKCS 12 (IIS)** .

3. Zadejte název importovaného certifikátu, například  `ContosoWilcardCert`

4. Vyberte **možnost zvolit soubor** a vyhledejte webový certifikát SSL, který bude odpovídat názvu subjektu, který chcete použít pro publikované služby.

5. Zadejte **heslo** pro importovaný certifikát a pak vyberte **importovat** .

6. V levém navigačním panelu přejdete na profily **místních přenosů**  >    >  **SSL**  >  **klient** a pak vyberte **vytvořit** .

7. Na stránce **Nový profil SSL klienta** zadejte jedinečný popisný název nového profilu SSL klienta a ujistěte se, že je nadřazený profil nastavený na **Clientssl** .

![Obrázek ukazuje aktualizace Big-IP](./media/f5ve-deployment-plan/client-ssl.png)

8. Zaškrtněte políčko úplně vpravo v řádku **řetězu klíčů certifikátu** a vyberte **Přidat** .

9. V třech rozevíracích seznamech vyberte certifikát se zástupnými znaky, který jste importovali bez hesla, a pak vyberte **Přidat**  >  **dokončeno**.

![Obrázek ukazuje, že se má aktualizovat zástupný znak společnosti Contoso pro Big-IP](./media/f5ve-deployment-plan/contoso-wildcard.png)

10. Opakujte kroky 6-9 pro vytvoření **profilu certifikátu serveru SSL**. Na horním pásu karet vyberte **SSL**  >  **Server**  >  **vytvořit**.

11. Na stránce **Nový profil SSL serveru** zadejte jedinečný popisný název nového profilu SSL serveru a ujistěte se, že je nadřazený profil nastavený na **serverssl** .

12. Zaškrtněte políčko úplně vpravo u certifikátu a klíčového řádku a v rozevíracím seznamu Vyberte importovaný certifikát a potom **dokončený**.

![Obrázek ukazuje aktualizovat server Big-IP Server All Profile](./media/f5ve-deployment-plan/server-ssl-profile.png)

>[!NOTE]
>Nedespairte-li si certifikát SSL nemůžete pořídit, můžete použít integrovaný server BIG-IP a klientské certifikáty SSL podepsaný svým držitelem. V prohlížeči se zobrazí jenom Chyba certifikátu.

Jedním z konečných kroků při přípravě silné IP adresy pro SHA je zajistit, aby bylo možné najít prostředky, které se publikují, a také adresářová služba, na které se spoléhá na jednotné přihlašování. Velká IP adresa má dva zdroje překladu názvů počínaje místním/.../hostem, nebo pokud záznam nenalezne, používá systém BIG-IP, na kterém je nakonfigurovaná služba DNS. Metoda souboru Hosts se nevztahuje na uzly a fondy APM, které používají plně kvalifikovaný název domény.

1. Ve webové konfiguraci přejít na konfigurace **systému**  >    >    >  **DNS** zařízení

2. V **seznamu vyhledávací server DNS** zadejte IP adresu vašeho serveru DNS prostředí.

3. Vyberte **Přidat**  >  **aktualizaci** .

V rámci samostatného a volitelného kroku můžete zvážit [konfiguraci protokolu LDAP](https://somoit.net/f5-big-ip/authentication-using-active-directory) pro ověřování správce velkých IP adres ve službě AD namísto správy místních účtů Big-IP.

## <a name="update-big-ip"></a>Aktualizace BIG-IP

Váš virtuální počítač s velkou IP adresou by se měl aktualizovat, aby se odemkly všechny nové funkce [popsané v pokynech založených na scénářích](f5-aad-integration.md). Na BIG-IPs název hostitele v levém horním rohu hlavní stránky můžete zaškrtnout možnost systémy, ve kterých se TMOS verze myši. Doporučuje se, abyste spustili v15. x a novější, které si můžete [stáhnout z F5](https://downloads.f5.com/esd/productlines.jsp). Použijte [pokyny](https://support.f5.com/csp/article/K34745165) k aktualizaci hlavního operačního systému (TMOS).

Pokud nemůžete aktualizovat hlavní TMOS, zvažte, jestli jste pomocí těchto kroků provedli aspoň upgrade samotné konfigurace s asistencí.

1. Z hlavní karty ve webové konfiguraci Big-IP přejděte na **přístup**  >  **s asistencí konfigurace** .

2. Na stránce **s asistencí** vyberte možnost **upgradovat konfiguraci s asistencí** .

![Obrázek ukazuje, jak aktualizovat Big-IP](./media/f5ve-deployment-plan/update-big-ip.png)

3. V dialogovém okně **konfigurace s asistencí pro upgrade** **Zvolte soubor** pro nahrání staženého případu použití a vyberte **nahrát a nainstalovat** .

4. Po dokončení upgradu vyberte **pokračovat**.

## <a name="backup-big-ip"></a>Zálohovat BIG-IP

Díky současnému zřízení systému BIG-IP doporučujeme, abyste provedli úplnou zálohu své konfigurace:

1. Přejít na **systémové**  >  **archivy**  >  **vytvořit**

2. Zadejte jedinečný **název souboru** a povolte **šifrování** pomocí přístupového hesla.

3. Nastavení možnosti **privátních klíčů** , které se mají **Zahrnout** taky k zálohování certifikátů zařízení a SSL

4. Vyberte **dokončeno** a počkejte na dokončení procesu.

5. Zobrazí se stav operace, který bude poskytovat stav zálohování. Vyberte **OK**.

6. Uložte místně archiv pro konfigurační sadu uživatele (UCS) tak, že kliknete na odkaz zálohování a vyberete **Stáhnout**.

V případě volitelného kroku můžete také vytvořit zálohu celého systémového disku pomocí [snímků Azure](../../virtual-machines/windows/snapshot-copy-managed-disk.md), což na rozdíl od zálohování webové konfigurace by vedlo k testování mezi TMOS verzemi nebo návratem do nového systému.

```PowerShell
# Install modules
Install-module Az
Install-module AzureVMSnapshots

# Authenticate to Azure
Connect-azAccount

# Set subscription by Id
Set-AzContext -SubscriptionId ‘<Azure_Subscription_ID>’

#Create Snapshot
New-AzVmSnapshot -ResourceGroupName '<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>'

#List Snapshots
#Get-AzVmSnapshot -ResourceGroupName ‘<E.g.contoso-RG>'

#Get-AzVmSnapshot -ResourceGroupName ‘<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>' | Restore-AzVmSnapshot -RemoveOriginalDisk 

```

## <a name="restore-big-ip"></a>Obnovení BIG-IP

Obnovení BIG-IP se řídí podobným postupem zálohování a dá se použít taky k migraci konfigurací mezi virtuálními počítači s velkou IP adresou. Před importem zálohy by měly být dodrženy podrobnosti o podporovaných cestách upgradu.

1. Přejít na **systémové**  >  **archivy**

2. Buď zvolte odkaz na zálohu, kterou chcete obnovit, nebo vyberte tlačítko **nahrát** a přejděte k dříve uloženému archivu UCS, který není v seznamu.

3. Zadejte heslo pro zálohování a vyberte **obnovit** .

```PowerShell
# Authenticate to Azure
Connect-azAccount

# Set subscription by Id
Set-AzContext -SubscriptionId ‘<Azure_Subscription_ID>’

#Restore Snapshot
Get-AzVmSnapshot -ResourceGroupName '<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>' | Restore-AzVmSnapshot

```

>[!NOTE]
>V době psaní je rutina AzVmSnapshot omezená na obnovení nejnovějšího snímku na základě data. Snímky se ukládají do kořenového adresáře skupiny prostředků virtuálního počítače. Mějte na paměti, že obnovením snímků se restartuje virtuální počítač Azure, takže tento čas pečlivě Vypočítejte.

## <a name="additional-resources"></a>Další zdroje informací

-   [Resetování hesla BIG-IP ve službě Azure](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html)
    -   [Resetování hesla bez použití portálu](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html#reset-the-password-without-using-the-portal)

-   [Změna síťového rozhraní používaného pro správu BIG-IP](https://clouddocs.f5.com/cloud/public/v1/shared/change_mgmt_nic.html)

-   [Trasy v konfiguraci s jedním síťovým ADAPTÉRem](https://clouddocs.f5.com/cloud/public/v1/shared/routes.html)

-   [Microsoft Azure: waagent](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_waagent.html)

## <a name="next-steps"></a>Další kroky

Vyberte [scénář nasazení](f5-aad-integration.md) a spusťte implementaci.
