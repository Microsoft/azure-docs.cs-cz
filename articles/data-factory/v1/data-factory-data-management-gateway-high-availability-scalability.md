---
title: Vysoká dostupnost s Brána pro správu dat v Azure Data Factory | Microsoft Docs
description: Tento článek vysvětluje, jak můžete škálovat Brána pro správu dat tak, že přidáte další uzly a škálování až zvýšením počtu souběžných úloh, které můžou běžet na uzlu.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 2d636eb381710c5e8341e24af8cd157a627348ef
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37053832"
---
# <a name="data-management-gateway---high-availability-and-scalability-preview"></a>Brána pro správu dat – vysokou dostupnost a škálovatelnost (Preview)
> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory. Pokud používáte aktuální verze služby Data Factory, přečtěte si téma [hostovanou na vlastním integrace runtime v](../create-self-hosted-integration-runtime.md). 


Tento článek vám pomůže nakonfigurovat vysokou dostupnost a škálovatelnost řešení s brány pro správu dat nebo integrace.    

> [!NOTE]
> Tento článek předpokládá, že jste již obeznámeni s základní informace o integraci Runtime (starší Data Management Gateway). Pokud si nejste, přečtěte si téma [Brána pro správu dat](data-factory-data-management-gateway.md).

>**Tato funkce preview je oficiálně podporované na 2.12.xxxx.x verze brány pro správu dat a vyšší**. Zkontrolujte, zda používáte verzi 2.12.xxxx.x nebo vyšší. Stáhněte si nejnovější verzi brány pro správu dat [zde](https://www.microsoft.com/download/details.aspx?id=39717).

## <a name="overview"></a>Přehled
Můžete přidružit brány správy dat, které jsou nainstalovány ve více počítačích na místě s jednou bránou logické z portálu. Tyto počítače se označují jako **uzly**. Může mít až **čtyři uzly** přidružený logické brány. Výhody s více uzly (místní počítače s nainstalovanou bránu) pro logické brány jsou:  

- Zlepšení výkonu přesun dat mezi místními a cloudovými datová úložiště.  
- Pokud jeden z uzlů ocitne mimo provoz z nějakého důvodu, jsou stále k dispozici pro přesun dat dalších uzlů. 
- Pokud jeden z uzlů potřebovat do režimu offline kvůli údržbě, jsou stále k dispozici pro přesun dat dalších uzlů.

Můžete taky nakonfigurovat počet **úlohy Přesun souběžných datového** , můžete spustit na uzlu škálování schopnosti produktu přesouvání dat mezi místními a cloudovými datová úložiště. 

Pomocí portálu Azure, můžete monitorovat stav tyto uzly, který vám pomůže zjistit, zda chcete přidat nebo odebrat uzel z logické brány. 

## <a name="architecture"></a>Architektura 
Následující obrázek poskytuje přehled architektury škálovatelnosti a dostupnosti funkce Brána pro správu dat: 

![Brána pro správu dat – vysoké dostupnosti a škálovatelnosti](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-high-availability-and-scalability.png)

A **logické brány** bránu přidáte do služby data factory na webu Azure portal. Dříve může přidružíte jenom jeden počítač Windows místní brána pro správu dat nainstalované s logické brány. To místního počítače brány se nazývá uzel. Nyní můžete přiřadit až **čtyři fyzické uzly** s logické brány. Je volána logické brány s několika uzly **několika uzly brány**.  

Všechny tyto uzly jsou **active**. Všechny zpracovávat úlohy přesunu dat pro přesun dat mezi místními a cloudovými datová úložiště. Jeden z uzlů fungují jako dispečera a pracovního procesu. Další uzly ve skupinách jsou uzlů pracovního procesu. A **dispečera** uzel vrátí data přesun úloh a úloh z cloudové služby a odešle je k pracovním uzlům (včetně samotného). A **pracovní** uzel provádí úlohy přesunu dat pro přesun dat mezi místními a cloudovými datová úložiště. Jsou všechny uzly pracovních procesů. Jenom jeden uzel může být odesílání a pracovního procesu.    

Obvykle můžete začít s jedním uzlem a **škálovat** přidat další uzly jako existující uzly jsou zahlcen zatížení přesun dat. Můžete také **škálovat** funkce přesun dat uzlu brány zvýšením počtu souběžných úloh, které je povoleno spustit na uzlu. Tato funkce je také dostupná s jedním uzlem bránou (i když není povolená funkce škálovatelnost a dostupnost). 

Brána s několika uzly udržuje údaje k úložišti dat. synchronizace pro všechny uzly. Pokud nastane problém s připojením mezi uzly, může být synchronizován přihlašovací údaje. Když nastavíte přihlašovací údaje pro úložiště dat místně, který používá bránu, uloží pověření v dispečeru nebo pracovním uzlu. Synchronizace dispečera uzlu s ostatními uzly pracovního procesu. Tento proces se označuje jako **pověření synchronizace**. Komunikační kanál mezi uzly mohou být **šifrované** pomocí veřejného certifikátu SSL/TLS. 

## <a name="set-up-a-multi-node-gateway"></a>Nastaví bránu několika uzly
V této části se předpokládá, že jste prošli následující dva články nebo neznáte koncepty v těchto článcích: 

- [Brána pro správu dat](data-factory-data-management-gateway.md) -obsahuje podrobný přehled brány.
- [Přesun dat mezi místní a cloudové úložiště dat](data-factory-move-data-between-onprem-and-cloud.md) – obsahuje návod podrobné pokyny pro bránu pomocí jednoho uzlu.  

> [!NOTE]
> Před instalací Brána pro správu dat na systému Windows na místním počítači, najdete v části předpoklady uvedené v [hlavní článek](data-factory-data-management-gateway.md#prerequisites).

1. V [návod](data-factory-move-data-between-onprem-and-cloud.md#create-gateway), při vytvoření logické brány, povolte **vysokou dostupnost a škálovatelnost** funkce. 

    ![Brána pro správu dat - povolit vysokou dostupnost a škálovatelnost](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-enable-high-availability-scalability.png)
2. V **konfigurace** stránky, použijte buď **Expresní instalace** nebo **ruční instalace** odkaz na nainstalovat bránu na prvním uzlu (se počítač Windows místní).

    ![Brána pro správu dat – express nebo ruční instalace](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-express-manual-setup.png)

    > [!NOTE]
    > Pokud použijete možnost Expresní instalace, komunikace mezi uzly se provádí bez šifrování. Název uzlu je stejný jako název počítače. Pokud komunikaci mezi uzly musí být šifrované nebo pokud chcete zadat název uzlu podle vaší volby, použijte ruční instalaci. Názvy nelze upravit později.
3. Pokud se rozhodnete **Expresní instalace**
    1. Po úspěšné instalaci brány zobrazí se následující zpráva:

        ![Brána pro správu dat – úspěch Expresní instalace](media/data-factory-data-management-gateway-high-availability-scalability/express-setup-success.png)
    2. Spusťte Správce konfigurace správy dat pro bránu pomocí následujících [tyto pokyny](data-factory-data-management-gateway.md#configuration-manager). Zobrazí název brány, název uzlu, stav, atd.

        ![Brána pro správu dat – instalace proběhla úspěšně.](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)
4. Pokud se rozhodnete **ruční instalaci**:
    1. Stažení instalačního balíčku z webu Microsoft Download Center, spusťte ji se nainstalovat bránu na váš počítač.
    2. Použití **ověřovací klíč** z **konfigurace** stránky k registraci brány.
    
        ![Brána pro správu dat – instalace proběhla úspěšně.](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-authentication-key.png)
    3. V **nový uzel brány** stránky, můžete zadat vlastní **název** uzlu brány. Ve výchozím nastavení je stejný jako název počítače název uzlu.    

        ![Brána pro správu dat – zadejte název](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-name.png)
    4. Na další stránce, můžete zvolit ohledně **povolit šifrování pro komunikaci mezi uzly**. Klikněte na tlačítko **přeskočit** můžete zakázat šifrování (výchozí).

        ![Brána pro správu dat - povolit šifrování](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-node-encryption.png)  
    
        > [!NOTE]
        > Změna režimu šifrování je podporována pouze když máte velké uzlu v logické brány. Chcete-li změnit režim šifrování, když brána má více uzlů, proveďte následující kroky: Odstraňte všechny uzly s výjimkou jeden uzel, změňte režim šifrování a poté znovu přidejte uzly.
        > 
        > V tématu [požadavky na certifikát protokolu TLS/SSL](#tlsssl-certificate-requirements) bodu seznam požadavků pro používání certifikát protokolu TLS/SSL. 
    5. Po úspěšné instalaci brány, klikněte na tlačítko spustit nástroje Configuration Manager:
    
        ![Ruční instalaci - spuštění nástroje configuration manager](media/data-factory-data-management-gateway-high-availability-scalability/manual-setup-launch-configuration-manager.png)   
    6. Správce konfigurace brány pro správu dat se zobrazí v uzlu (místní počítač Windows), který se zobrazuje stav připojení, **název brány**, a **název uzlu**.  

        ![Brána pro správu dat – instalace proběhla úspěšně.](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)

        > [!NOTE]
        > Pokud zřizujete brány na virtuální počítač Azure, můžete použít [této šablony Azure Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/101-mutiple-vms-with-data-management-gateway). Tento skript vytvoří logické brány, nastaví virtuální počítače s nainstalovaným softwarem Brána pro správu dat a registruje je logické brány. 
6. Na portálu Azure, spusťte **brány** stránky: 
    1. Na objekt pro vytváření dat domovské stránce portálu, klikněte na tlačítko **propojené služby**.
    
        ![Domovská stránka objektu pro vytváření dat](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-home-page.png)
    2. Vyberte **brány** zobrazíte **brány** stránky:
    
        ![Domovská stránka objektu pro vytváření dat](media/data-factory-data-management-gateway-high-availability-scalability/linked-services-gateway.png)
    4. Zobrazí **brány** stránky:   

        ![Brána s jedním uzlem zobrazení](media/data-factory-data-management-gateway-high-availability-scalability/gateway-first-node-portal-view.png) 
7. Klikněte na tlačítko **přidat uzel** na panelu nástrojů do logické brány přidat uzel. Pokud máte v úmyslu použít Expresní instalace, proveďte tento krok z místního počítače, který bude přidán jako uzel k bráně. 

    ![Brána pro správu dat – přidat uzel nabídky](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)
8. Kroky jsou podobné nastavení prvního uzlu. Uživatelské rozhraní nástroje Configuration Manager umožňuje nastavit název uzlu, pokud si zvolíte možnost Ruční instalace: 

    ![Configuration Manager – instalace druhé brány](media/data-factory-data-management-gateway-high-availability-scalability/install-second-gateway.png)
9. Po úspěšné instalaci brány v uzlu nástroj Configuration Manager zobrazí následující obrazovka:  

    ![Configuration Manager – úspěšné instalaci druhé brány](media/data-factory-data-management-gateway-high-availability-scalability/second-gateway-installation-successful.png)
10. Pokud otevřete **brány** stránky na portálu, byste nyní vidět dva uzly brány: 

    ![Brána s dvěma uzly na portálu](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)
11. Pokud chcete odstranit uzel brány, klikněte na tlačítko **odstranit uzlu** na panelu nástrojů vyberte uzel, kterou chcete odstranit a potom klikněte na **odstranit** na panelu nástrojů. Tato akce odstraní vybraný uzel ze skupiny. Všimněte si, že tato akce neodinstaluje software brány pro správu dat z uzlu (místní počítač Windows). Použití **přidat nebo odebrat programy** v Ovládacích panelech na místní odinstalace gateway. Když odinstalujete z uzlu brány, automaticky se odstraní na portálu.   

## <a name="upgrade-an-existing-gateway"></a>Upgrade existující bránu
Můžete upgradovat existující bránu používat funkci vysokou dostupnost a škálovatelnost. Tato funkce funguje jenom s uzly, které mají Brána pro správu dat verze > = 2.12.xxxx. Zobrazí verze brány pro správu dat nainstalovány na počítači, v **pomoci** karta nástroje správu Správce konfigurace brány dat. 

1. Aktualizujte bránu na místní počítač na nejnovější verzi tak, že následující stažením a systémem MSI instalační balíček z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). V tématu [instalace](data-factory-data-management-gateway.md#installation) podrobnosti.  
2. Přejděte na portál Azure. Spusťte **stránka objektu pro vytváření dat** data Factory. Klikněte na dlaždici propojené služby spustíte **stránka propojené služby**. Vyberte bránu, spusťte **brány stránky**. Klikněte na možnost a povolte **funkce ve verzi Preview** jak je znázorněno na následujícím obrázku: 

    ![Brána pro správu dat - povolit funkce ve verzi preview](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-existing-gateway-enable-high-availability.png)   
2. Jakmile je funkce preview na portálu, zavřete všechny stránky. Otevřete **brány stránky** zobrazíte nové uživatelské rozhraní (UI) preview.
 
    ![Brána pro správu dat – úspěch funkce verze preview povolit](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview-success.png)

    ![Brána pro správu dat – náhled uživatelského rozhraní](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview.png)

    > [!NOTE]
    > Během upgradu název prvního uzlu je název počítače. 
3. Nyní přidejte do uzlu. V **brány** klikněte na tlačítko **přidat uzel**.  

    ![Brána pro správu dat – přidat uzel nabídky](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)

    Postupujte podle pokynů z předchozí části nastavit uzlu. 

### <a name="installation-best-practices"></a>Osvědčené postupy instalace

- Schéma napájení nakonfigurujte na hostitelském počítači pro bránu, aby počítač nepřejde do režimu spánku. Pokud hostitelský počítač přejde do režimu spánku, brána neodpovídá na požadavky na data.
- Zálohujte certifikát přidružený k bráně.
- Zkontrolujte, zda že jsou všechny uzly podobné konfigurace (doporučeno) ideální výkonu. 
- Přidejte aspoň dva uzly, abyste zajistili vysokou dostupnost.  

### <a name="tlsssl-certificate-requirements"></a>Požadavky na certifikát protokolu TLS/SSL
Tady jsou požadavky na certifikát TLS/SSL, který se používá k zabezpečení komunikace mezi integrace modulu runtime uzly:

- Certifikát musí být veřejně důvěryhodné X509 v3 certifikátu. Doporučujeme vám, že používáte certifikáty vydané veřejnou (třetích stran) certifikační autoritou (CA).
- Každý uzel runtime integrace musí důvěřovat tento certifikát, stejně jako na klientský počítač, který je spuštěna aplikace Správce přihlašovacích údajů. 
> [!NOTE]
> Aplikace Správce přihlašovacích údajů se používá při bezpečně nastavení přihlašovacích údajů z Průvodce kopírováním nebo portálu Azure. A může to být přímým z libovolného počítače ve stejné síti jako místní / privátní úložiště dat.
- Zástupný znak certifikáty jsou podporovány. Pokud je název vaší plně kvalifikovaný název domény **node1.domain.contoso.com**, můžete použít ***. domain.contoso.com** jako název subjektu certifikátu.
- Vzhledem k tomu, že se použije pouze poslední položky alternativní názvy subjektu a všechny ostatní bude ignorován kvůli aktuálním omezením, nedoporučuje se používat certifikátů SAN. Například máte certifikát SAN, jejichž SAN jsou **node1.domain.contoso.com** a **node2.domain.contoso.com**, můžete použít pouze tohoto certifikátu na počítače, jejichž plně kvalifikovaný název domény **node2.domain.contoso.com**.
- Podporuje všechny klíče velikost podporovaná technologií Windows Server 2012 R2 pro certifikáty SSL.
- Certifikát pomocí CNG klíče nejsou podporovány. Doesrted DoesDoes nepodporuje certifikáty, které používají klíči CNG.

#### <a name="faq-when-would-i-not-enable-this-encryption"></a>Nejčastější dotazy: Když nebude povolit toto šifrování?
Povolení šifrování můžete přidat určité náklady na infrastrukturu (vlastnícím veřejný certifikát). proto může přeskočit v povolení šifrování níže případech:
- Pokud se modul runtime integrace běží v důvěryhodné síti, nebo v síti s transparentní šifrování jako IP za sekundu. Vzhledem k tomu, že je tento komunikační kanál mezi jenom omezené v rámci vaší důvěryhodné síti, možná nebudete potřebovat další šifrování.
- Modul runtime integrace není při spuštění v produkčním prostředí. To může pomoct snížit náklady na certifikátu TLS/SSL.


## <a name="monitor-a-multi-node-gateway"></a>Monitorování několika uzly brány
### <a name="multi-node-gateway-monitoring"></a>Monitorování několika uzly brány
Na portálu Azure můžete zobrazit skoro v reálném čase snímek využití prostředků (procesoru, paměti, network(in/out) atd.) na každém uzlu společně s stavy uzlů brány. 

![Brána pro správu dat – více monitorování uzlu](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)

Můžete povolit **upřesňující nastavení** v **brány** stránky najdete v části Upřesnit metriky jako **sítě**(vstup/výstup), **Role & pověření stav**, což je užitečné při ladění problémů s brány, a **souběžných úloh** (spuštění / omezit) který může být upravený / odpovídajícím způsobem změněné během optimalizace výkonu. Následující tabulka obsahuje popisy sloupců **uzly brány** seznamu:  

Vlastnost monitorování | Popis
:------------------ | :---------- 
Název | Název logické brány a uzly asociovaným s bránou.  
Status | Stav logické brány a uzly brány. Příklad: Online nebo Offline nebo Limited/atd. Informace o těchto stavů najdete v tématu [stav brány](#gateway-status) části. 
Verze | Zobrazuje verzi logické brány a každý uzel brány. Verze logické brány je určen na základě verze Většina uzlů ve skupině. Pokud nejsou správně uzly s různými verzemi v nastavení logické brány, pouze uzly se číslo verze jako funkce logické brány. Ostatní jsou v režimu omezené a je nutné ručně aktualizovat (pouze v případě automatické aktualizace nezdaří). 
Dostupná paměť | Dostupná paměť na uzel brány. Tato hodnota je snímku near v reálném čase. 
Využití procesoru | Využití procesoru uzlu brány. Tato hodnota je snímku near v reálném čase. 
Sítě (vstup/výstup) | Využití uzlu brány sítě. Tato hodnota je snímku near v reálném čase. 
Souběžné úlohy (spuštění / omezit) | Počet úlohy nebo úlohy na jednotlivých uzlech spuštěné. Tato hodnota je snímku near v reálném čase. Limit označuje, že maximální souběžných úloh pro každý uzel. Tato hodnota je definována v závislosti na velikosti počítače. Můžete zvýšit limit škálování provádění souběžné úlohy v pokročilých scénářích, kde procesoru nebo paměti / síť je v části využívat, ale aktivity jsou vypršení časového limitu. Tato funkce je také dostupná s jedním uzlem bránou (i když není povolená funkce škálovatelnost a dostupnost). Další informace najdete v tématu [škálování aspekty](#scale-considerations) části. 
Role | Existují dva typy rolí – dispečera a pracovního procesu. Všechny uzly jsou pracovníci, což znamená, že se všechny slouží k provedení úlohy. Je pouze jeden uzel dispečera, který se používá k přijetí změn úlohy nebo úlohy z cloudové služby a jejich odesílání na jiný pracovní uzly (včetně samotného). 

![Brána pro správu dat – pokročilé monitorování více uzlu](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-advanced.png)

### <a name="gateway-status"></a>Stav brány.

Následující tabulka obsahuje možné stavy z **uzel brány**: 

Status  | Komentáře nebo scénáře
:------- | :------------------
Online | Uzel připojen ke službě Data Factory.
Offline | Uzel je offline.
Upgrade | Uzel, která má být automaticky aktualizován.
Omezená | Problém s připojením. Může být kvůli problému 8050 portu HTTP, problém s připojením služby sběrnice nebo problémům synchronizace přihlašovacích údajů. 
Neaktivní | Uzel je v konfiguraci se liší od konfigurace jiných Většina uzlů.<br/><br/> Uzlem může být neaktivní, když se nemůže připojit k jiné uzly. 


Následující tabulka obsahuje možné stavy z **logické brány**. Stav brány. závisí na stavy, které jsou uzly brány. 

Status | Komentáře
:----- | :-------
Nutná registrace | K této logické brány je ještě zaregistrován žádný uzel.
Online | Brána uzly jsou online
Offline | Žádný uzel ve stavu online.
Omezená | Ne všechny uzly v této brány jsou v dobrém stavu. Tento stav se upozornění, že některé uzel může být mimo provoz! <br/><br/>Může být kvůli problémům synchronizace přihlašovacích údajů na dispečera nebo pracovního uzlu. 

### <a name="pipeline-activities-monitoring"></a>Kanál / monitorování aktivity
Portál Azure poskytuje kanálu monitorování zkušenosti s granulární uzlu úrovně podrobností. Například uvádí, které aktivity byl spuštěn na uzel. Tyto informace můžete být užitečné porozumět problémům s výkonem v konkrétním uzlu, můžete z důvodu omezení šířky pásma sítě. 

![Brána pro správu dat – sledování pro kanály více uzlů.](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-pipelines.png)

![Brána pro správu dat – podrobnosti o kanálu](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-pipeline-details.png)

## <a name="scale-considerations"></a>Důležité informace o škálování

### <a name="scale-out"></a>Horizontální navýšení kapacity
Když **zbývá málo dostupné paměti** a **využití procesoru je Vysoká**, přidání nového uzlu pomáhá škálování zatížení mezi počítači. Pokud aktivity selhávají v důsledku vypršení časového limitu nebo brány uzlu je offline, je užitečné, pokud přidat uzel do brány.
 
### <a name="scale-up"></a>Vertikální navýšení kapacity
Pokud nejsou dobře využité dostatek paměti a procesoru, ale nečinnosti kapacita je 0, by měl škálovat zvýšením počtu souběžných úloh, které můžou běžet na uzlu. Můžete také vertikálně navýšit kapacitu aktivity jsou vypršení časového limitu, protože je přetížena brány. Jak je znázorněno na následujícím obrázku, můžete zvýšit maximální kapacita pro uzel. Doporučujeme ji spustit s zvýší.  

![Brána pro správu dat – aspekty škálování](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-scale-considerations.png)


## <a name="known-issuesbreaking-changes"></a>Známé problémy nebo nejnovější změny

- V současné době může mít až čtyři fyzické zprostředkovatelských uzlů zasílání zpráv pro jednu logickou bránu. Pokud potřebujete více než čtyři uzly z důvodů výkonu, odešlete e-mail na [ DMGHelp@microsoft.com ](mailto:DMGHelp@microsoft.com).
- Nemůžete se znovu zaregistrovat uzel brány s ověřovací klíč z jiné logické brány přepnutí z aktuální logické brány. Pokud chcete znovu zaregistrovat, bránu odinstalovat z uzlu, přeinstalujte brány a zaregistrovat ji pomocí ověřovací klíč pro jiné logické brány. 
- Pokud server proxy protokolu HTTP je potřeba pro všechny uzly brány, nastavení proxy serveru v diahost.exe.config a diawp.exe.config a ujistěte se, že všechny uzly mají stejnou diahost.exe.config a diawip.exe.config pomocí Správce serveru. V tématu [konfigurace nastavení proxy serveru](data-factory-data-management-gateway.md#configure-proxy-server-settings) podrobnosti. 
- Chcete-li změnit režim šifrování pro komunikaci mezi uzly ve Správci konfigurace brány, odstraňte všechny uzly v portálu kromě jednoho. Pak přidáte uzly zpět po změně režimu šifrování.
- Pokud zvolíte možnost šifrování kanálu komunikaci mezi uzly, použijte oficiální certifikátu protokolu SSL. Certifikát podepsaný svým držitelem může způsobit problémy s připojením, protože stejný certifikát není důvěryhodný v seznamu certifikační autority na jiné počítače. 
- Nelze zaregistrovat uzel brány do logické brány, pokud uzel verze je nižší než verze logické brány. Odstranit všechny uzly logické brány z portálu, tak, aby se můžete zaregistrovat nižší verze node(downgrade) ho. Pokud odstraníte všechny uzly logické brány, ručně nainstalujte a zaregistrujte nových uzlů k této logické brány. Expresní instalace není v tomto případě nepodporuje.
- Nelze použít Expresní instalace nainstalovat uzly do stávající logické brána, která je stále pomocí pověření cloudu. Můžete zkontrolovat, kde jsou přihlašovací údaje uložené ze Správce konfigurace brány na kartě nastavení.
- Expresní instalace nelze použít k instalaci uzly do stávající logické brány, který má povolené šifrování mezi uzly. Nastavení režimu šifrování zahrnuje ručně přidejte certifikáty, je Expresní instalace žádné další možnost. 
- Pro kopírování souborů z místního prostředí, neměli byste používat \\localhost nebo C:\files už od localhost nebo místní jednotku možná není přístupný prostřednictvím všech uzlů. Místo toho použijte \\ServerName\files a zadejte umístění souborů.


## <a name="rolling-back-from-the-preview"></a>Vrácení zpět z verze preview 
Vrácení z verze preview, odstraňte všechny uzly, ale jeden uzel. Nezávisle na tom, které uzly odstranit, ale ujistěte se, že máte nejméně jeden uzel v logické brány. Odinstalovává se brána na počítači nebo pomocí portálu Azure, můžete odstranit uzlu. Na portálu Azure v **Data Factory** klikněte na tlačítko propojené služby spustíte **propojené služby** stránky. Vyberte bránu, spusťte **brány** stránky. Na stránce brány najdete v uzlu asociovaným s bránou. Stránce můžete odstranit z brány v uzlu.
 
Po odstranění, klikněte na tlačítko **funkce verze preview** ve stejné stránky portálu Azure a zakažte funkci náhledu. Resetujete bránu pro jeden uzel GA (Obecné dostupnosti) brány.


## <a name="next-steps"></a>Další postup
Projděte si následující články:
- [Brána pro správu dat](data-factory-data-management-gateway.md) -obsahuje podrobný přehled brány.
- [Přesun dat mezi místní a cloudové úložiště dat](data-factory-move-data-between-onprem-and-cloud.md) – obsahuje návod podrobné pokyny pro bránu pomocí jednoho uzlu. 
