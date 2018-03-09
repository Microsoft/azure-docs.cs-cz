---
title: "Ochrana osobních dat pomocí funkce zabezpečení sítě Azure | Microsoft Docs"
description: "Ochrana osobních dat pomocí funkce zabezpečení sítě Azure. To může sloužit k dosažení souladu s obecné Data Protection nařízení (GDPR)"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2018
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 54aeb22a501e248105931df341d23e524448155a
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="protect-personal-data-with-network-security-features-azure-application-gateway-and-network-security-groups"></a>Ochrana osobních dat pomocí funkce zabezpečení sítě: Azure Application Gateway a skupiny zabezpečení sítě

Tento článek obsahuje informace a postupy, které vám pomohou používat Azure Application Gateway a skupiny zabezpečení sítě na ochranu osobních údajů. Tyto informace mohou být užitečné v vaše snaze v souladu s obecné Data Protection nařízení (GDPR).

Důležitým prvkem v strategie víceúrovňová zabezpečení k ochraně osobních údajů osobních údajů je obrana proti běžné zneužití ohrožení zabezpečení, například Injektáž SQL nebo skriptování mezi servery. Zachování nežádoucí síťový provoz z vaší Azure virtuální sítě přispívá k ochraně proti potenciální ohrožení citlivých dat a Microsoft Azure nabízí nástroje, které pomáhají chránit vaše data proti útočníkům.

## <a name="scenario"></a>Scénář

Velké výletních společnosti, centrálou ve Spojených státech amerických, je rozšířit jeho operací a nabídnout itineráře v Středomoří, Jaderského a baltský moři, jakož i Britské ostrovy. Při podpoře těchto úsilí získala menší výletních Víceřádkový na základě v Itálii, Německo, Dánsko a Spojeném království

Společnost používá Microsoft Azure k ukládání firemních dat v cloudu a spouštět aplikace na virtuální počítače, které zpracovat a přístup k těmto datům. Tato data obsahují osobní identifikovatelné údaje, například jména, adresy, telefonních čísel a informace o kreditní kartě z jeho základní globální zákazníka. Ve všech umístěních zahrnuje také tradiční informace lidských zdrojů, jako jsou adresy, telefonních čísel, daň identifikační čísla a další informace o zaměstnance společnosti. Na řádku výletních také udržuje velké databáze potřebu a věrnost program členů, která zahrnuje osobní údaje ke sledování vztahů se zákazníky aktuální a starší.

Zaměstnanci společnosti přístup k síti ze vzdálených pobočkách společnosti a agenty cesta umístěné po celém světě mají přístup k některým prostředkům společnosti a pracovat s ním pomocí webové aplikace hostované ve virtuálních počítačích Azure.

## <a name="problem-statement"></a>Popis problému

Společnosti musí chránit ochranu osobních údajů zákazníků a zaměstnanců osobních dat od útočníky, kteří zneužívají ohrožení zabezpečení softwaru ke spuštění škodlivého kódu, který mohla vystavit osobní data uložená nebo použít cloudových aplikací společnosti.

## <a name="company-goal"></a>Cílem společnosti

Cílem společnosti zajistit, že neoprávněné osoby přístup k podnikovým virtuálních sítí Azure a aplikace a data, která jsou umístěny existuje zneužitím známých chyb zabezpečení. 

## <a name="solutions"></a>Řešení

Microsoft Azure nabízí mechanismy zabezpečení, aby pomohly zabránit nežádoucí provoz zadávání virtuálních sítí Azure. Řízení příchozí a odchozí přenosy tradičně provádí brány firewall. V Azure můžete službu Application Gateway s brány Firewall webových aplikací a skupin zabezpečení sítě (NSG), které slouží jako jednoduchý distribuovanou bránou firewall. Tyto nástroje vám umožňují detekovat a blokovat nežádoucí síťový provoz.

### <a name="application-gatewayweb-application-firewall"></a>Brány Firewall aplikací brány nebo webové aplikace

[Brány Firewall webových aplikací](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) součást (firewall webových aplikací) [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) chrání webových aplikací, které jsou stále cíle nebezpečné útoky, které využívají známé běžné chyby zabezpečení. Centralizované firewall webových aplikací chrání před útoky na web a zjednodušuje správu zabezpečení bez nutnosti změny aplikace.

Různé kategorie útoku, včetně Injektáž SQL, skriptování mezi weby, porušení protokolu HTTP a anomálií, robotů, prohledávací moduly, skenerů, časté nesprávné konfigurace aplikace, HTTP Denial of Service a další běžné útoky, jako adresy Azure firewall webových aplikací příkaz vkládání, požadavek HTTP pašování, rozdělení odpovědi HTTP a útoky zahrnutí vzdáleného souboru. 

Můžete vytvořit služby application gateway s firewall webových aplikací nebo existující aplikační brány přidat firewall webových aplikací. V obou případech Azure Application Gateway vyžaduje vlastní podsíti.

#### <a name="how-do-i-create-an-application-gateway-with-waf"></a>Vytvoření služby application gateway s firewall webových aplikací 

Pokud chcete vytvořit novou aplikační bránu s povolen firewall webových aplikací, postupujte takto:

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. Vyberte **vytvořit prostředek** > **sítě** > **Application Gateway**.
5. V **Základy** okno, které se zobrazí, zadejte hodnoty pro následující pole: název, vrstvy (Standard nebo firewall webových aplikací), velikost SKU (malé, střední nebo velké) Instance počet (2 pro zajištění vysoké dostupnosti), předplatné, skupinu prostředků a umístění.
6. V **nastavení** okno, které se zobrazí pod **virtuální síť**, klikněte na tlačítko **vyberte virtuální síť**. Tento krok se otevře, zadejte v okně vyberte virtuální síť.

7. Klikněte na tlačítko **vytvořit nový** otevřete **vytvořit virtuální síť** okno.

8. Zadejte následující hodnoty: název, adresní prostor, název podsítě, rozsah adres podsítě. Klikněte na **OK**.

9. Na **nastavení** okno pod **konfigurace IP front-endu**, vyberte typ IP adresy.

10. Klikněte na tlačítko **zvolte veřejnou IP adresu,** pak **vytvořit nové.**

11. Přijměte výchozí hodnotu a klikněte na tlačítko **OK.**

12. Na **nastavení** okno pod **konfiguraci naslouchacího procesu**, vyberte pomocí protokolu HTTP nebo HTTPS v části **protokol**. K používání protokolu HTTPS, je požadovaný certifikát.

13. Konfigurovat konkrétní nastavení firewall webových aplikací: **brány Firewall stav** (**povoleno**) a **režimu brány Firewall** (**prevence**). Pokud se rozhodnete **detekce** režim, je provoz jenom protokolována.

14. Zkontrolujte **Souhrn** a klikněte na tlačítko **OK**. Službu application gateway je nyní zařazen do fronty a vytvořit.

Po vytvoření služby application gateway, můžete přejít na ni na portálu a pokračovat v konfiguraci služby application gateway.

![Vytvoření aplikační brány](media/protect-netsec/adatum-app-gateway.png)

#### <a name="how-do-i-add-waf-to-an-existing-application"></a>Jak přidat firewall webových aplikací na existující aplikaci?

Pokud chcete aktualizovat existující aplikační brány pro podporu firewall webových aplikací v režimu prevence, postupujte takto:

1. Na webu Azure Portal v podokně **Oblíbené** klikněte na **Všechny prostředky**.

2. Klikněte na existující aplikační brána v **všechny prostředky** okno. 
>[!NOTE]
Poznámka: Pokud odběr, který jste již vybrali neobsahuje několik prostředků, můžete zadat název ve filtru podle názvu... pro snadný přístup k zóně DNS.
3. Klikněte na tlačítko **brány firewall webových aplikací** a aktualizovat nastavení aplikační brány: **upgradujte firewall webových aplikací úrovně** (zaškrtnuto), **brány Firewall stav** (povoleno),  **Režimu brány firewall** (prevence). Musíte také nakonfigurovat sadu pravidel a nakonfigurovat zakázaná pravidla.

Podrobnější informace o tom, jak vytvořit novou aplikační bránu firewall webových aplikací a jak přidat firewall webových aplikací do existující aplikace bránu, najdete v části [vytvoření služby application gateway pomocí brány firewall webových aplikací pomocí portálu.](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal)

### <a name="network-security-groups"></a>Network Security Groups (Skupiny zabezpečení sítě)

A [skupinu zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG) obsahuje seznam pravidel zabezpečení, která povolují nebo odpírají síťový provoz prostředky připojenými k [virtuálních sítí Azure](https://docs.microsoft.com/azure/virtual-network/) (VNet). Skupiny Nsg můžou být přidružena k podsítě nebo jednotlivé virtuální počítače. Pokud je skupina zabezpečení sítě přidružená k podsíti, pravidla se vztahují na všechny prostředky, které jsou připojené k příslušné podsíti. Provoz se dá dále omezit přidružením skupiny zabezpečení sítě k virtuálnímu počítači nebo síťové kartě.

Skupiny Nsg obsahují čtyři vlastnosti: název, oblast, skupinu prostředků a pravidla.
>[!Note]
Ačkoli skupina zabezpečení sítě existuje ve skupině prostředků, dá se přidružit k prostředkům v libovolné skupině prostředků, pokud příslušný prostředek patří do stejné oblasti Azure jako příslušná skupina zabezpečení sítě.

Pravidla NSG obsahují devět vlastnosti: název, protokol (TCP, UDP nebo \*, což zahrnuje ICMP a také protokolu UDP a TCP), zdroj rozsah portů, rozsah cílových portů, zdrojová adresa předpony, předpona cílové adresy směr (příchozí nebo odchozí), () s prioritou rozsahu od 100 do 4096) a přístup k typu (povolit nebo zakázat). Všechny skupiny Nsg obsahují sadu výchozích pravidel, která je možné odstranit, nebo přepsat pravidly, které vytvoříte.

#### <a name="how-do-i-implement-nsgs"></a>Jak implementovat skupiny Nsg?

Implementací skupin Nsg vyžaduje plánování a existuje několik aspekty návrhu, které je třeba vzít v úvahu. Patří mezi ně omezení pro počet skupin Nsg na předplatné a pravidla na skupinu NSG; Virtuální síť a podsíť návrhu, zvláštní pravidla, provoz protokolu ICMP, izolace vrstev s podsítí, nástroje pro vyrovnávání zatížení a další.

Další pokyny plánování a implementace skupiny Nsg a vzorový scénář nasazení najdete v tématu [filtrování provozu sítě přenosů se skupinami zabezpečení sítě.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)

#### <a name="how-do-i-create-rules-in-an-nsg"></a>Vytvoření pravidla v skupinu NSG

Pokud chcete vytvořit příchozích pravidel v existující skupině, postupujte takto:

1. Klikněte na tlačítko **všechny služby**a potom **skupin zabezpečení sítě**.

2. V seznamu skupin Nsg, klikněte na **NSG front-endu**a potom **příchozí pravidla zabezpečení.**

3. V seznamu příchozí pravidla zabezpečení, klikněte na **přidat.**

4. Zadejte hodnoty do následujících polí: název, Priority, zdroj, protokol, zdrojový rozsah, cíl, cílový rozsah portů a akce.

Nové pravidlo se zobrazí v této skupině za několik sekund.

![pravidla zabezpečení sítě](media/protect-netsec/inbound-security.png)

Další pokyny o tom, jak vytvářet skupiny Nsg v podsítích, vytvořit pravidla a přidružit skupinu NSG k podsíti front-end a back-end, najdete v části [vytvoření skupin zabezpečení sítě pomocí portálu Azure.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal)

## <a name="next-steps"></a>Další postup

[Zabezpečení sítě Azure](https://azure.microsoft.com/blog/azure-network-security/)

[Osvědčené postupy zabezpečení sítě Azure](https://docs.microsoft.com/azure/security/azure-security-network-security-best-practices)

[Získání informací o skupinu zabezpečení sítě](https://docs.microsoft.com/rest/api/network/virtualnetwork/get-information-about-a-network-security-group)

[Brány firewall webových aplikací (firewall webových aplikací)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview)
