---
title: Vysoká dostupnost pomocí brány správy dat ve službě Azure Data Factory | Dokumentace Microsoftu
description: Tento článek vysvětluje, jak můžete horizontálně navýšit brány pro správu dat přidáním dalších uzlů a škálování nahoru zvýšením počtu souběžných úloh, které můžou běžet na uzlu.
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
ms.openlocfilehash: b9ec9867e9abd188142067e593c925e3c8acdd0b
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37113336"
---
# <a name="data-management-gateway---high-availability-and-scalability-preview"></a>Brána správy dat – vysoká dostupnost a škálovatelnost (Preview)
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [v místním prostředí integration runtime ve službě](../create-self-hosted-integration-runtime.md). 


Tento článek vám pomůže nakonfigurovat vysokou dostupnost a škálovatelnost řešení pomocí brány pro správu dat / integrace.    

> [!NOTE]
> Tento článek předpokládá, že jste už obeznámení se základy prostředí Integration runtime (starší Data správy brány). Pokud si nejste, přečtěte si téma [brána správy dat](data-factory-data-management-gateway.md).

>**Tato funkce ve verzi preview je oficiálně podporované na 2.12.xxxx.x verze brány správy dat a vyšší**. Ujistěte se prosím, že používáte verzi 2.12.xxxx.x nebo vyšší. Stáhněte si nejnovější verzi brány správy dat [tady](https://www.microsoft.com/download/details.aspx?id=39717).

## <a name="overview"></a>Přehled
Můžete přiřadit bran pro správu dat, které jsou nainstalované na víc místních počítačů s jednou bránou logické z portálu. Tyto počítače se nazývají **uzly**. Můžete mít až **čtyři uzly** přidružený k logické brány. Výhody s více uzly (na místních počítačích s nainstalovanou bránu) pro logické brány jsou:  

- Zlepšení výkonu při přesunu dat mezi místním prostředím a cloudem datových úložišť.  
- Pokud jeden z uzlů ocitne mimo provoz z nějakého důvodu, jsou stále k dispozici pro přesun dat jiných uzlech. 
- Pokud jeden z uzlů, je třeba provést offline kvůli údržbě, jsou stále k dispozici pro přesun dat jiných uzlech.

Můžete také nakonfigurovat počet **souběžných datových přesun úloh** , který lze spustit na uzlu vertikálně navýšit kapacitu funkci pro přesun dat mezi místním prostředím a cloudem datových úložišť. 

Pomocí webu Azure portal, můžete monitorovat stav těchto uzlů, který vám pomůže rozhodnout, jestli se má přidat nebo odebrat uzel z logické brány. 

## <a name="architecture"></a>Architektura 
Následující diagram představuje přehled architektury škálovatelnosti a dostupnosti funkce brány správy dat: 

![Brána správy dat – vysoká dostupnost a škálovatelnost](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-high-availability-and-scalability.png)

A **logické brány** představuje bránu přidáte do služby data factory na webu Azure Portal. Dříve může přidružíte pouze jeden počítač s Windows v místním brána správy dat nainstalovaná s logické brány. Tento místní počítač brány se nazývá uzel. Teď můžete přidružit až **čtyři fyzické uzly** s logické brány. Je volána logické brány s několika uzly **více uzly brány**.  

Všechny tyto uzly jsou **aktivní**. Všechny zpracování úlohy přesunu dat pro přesun dat mezi místním prostředím a cloudem datových úložišť. Jeden z uzlů fungují jako dispečer a pracovního procesu. Ostatní uzly ve skupinách se pracovní uzly. A **dispečer** uzlu si vyžádá data přesunu úlohy nebo úlohy z cloudové služby a odešle je do uzlů pracovního procesu (včetně samotného). A **pracovního procesu** uzel provádí úlohy přesunu dat pro přesun dat mezi místním prostředím a cloudem datových úložišť. Jsou všechny uzly pracovních procesů. Odeslání a pracovní proces může být jenom jeden uzel.    

Obvykle můžete začít s jedním uzlem a **horizontální navýšení kapacity** na přidání dalších uzlů jako stávající uzly jsou zahlcen načtení přesunu dat. Můžete také **vertikálně navýšit kapacitu** funkci přesunu dat uzlu brány zvýšením počtu souběžných úloh, které je možné spustit na uzlu. Tato možnost je také dostupné s jedním uzlem bránou (i když není povolená funkce, škálovatelnost a dostupnost). 

Brána s více uzly uchovává přihlašovací údaje úložiště dat synchronizované napříč všemi uzly. Pokud nastane problém s připojením mezi uzly, přihlašovací údaje mohou být synchronizovaný. Když nastavíte přihlašovací údaje pro na místního úložiště dat, která používá bránu, uloží pověření na dispečerský/pracovní uzel. Uzel dispečer se synchronizuje s jiných pracovních uzlů. Tento proces se označuje jako **pověření synchronizace**. Komunikační kanál mezi uzly mohou být **šifrované** pomocí veřejného certifikátu SSL/TLS. 

## <a name="set-up-a-multi-node-gateway"></a>Nastavení brány několika uzly
V této části se předpokládá, že jste prošli články nebo neznáte pojmy v těchto článcích: 

- [Brána správy dat](data-factory-data-management-gateway.md) – poskytuje podrobný přehled o brány.
- [Přesun dat mezi místním a cloudovým úložištím dat](data-factory-move-data-between-onprem-and-cloud.md) – obsahuje návod s podrobnými pokyny pro použití brány se jeden uzel.  

> [!NOTE]
> Před instalací brány pro správu dat na počítači s Windows v místním, naleznete v tématu požadavky uvedené v [hlavní článek](data-factory-data-management-gateway.md#prerequisites).

1. V [návod](data-factory-move-data-between-onprem-and-cloud.md#create-gateway), při vytvoření logické brány, povolte **vysoká dostupnost a škálovatelnost** funkce. 

    ![Brána správy dat – povolit vysokou dostupnost a škálovatelnost](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-enable-high-availability-scalability.png)
2. V **konfigurovat** stránky, použijte buď **Expresní instalace** nebo **ruční instalaci** odkazu na instalaci brány na první uzel (v místním Windows počítače s).

    ![Brána správy dat – výslovných, nebo ruční instalace](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-express-manual-setup.png)

    > [!NOTE]
    > Pokud použijete možnost Expresní instalace, probíhá komunikace mezi uzly bez šifrování. Název uzlu je stejný jako název počítače. Ruční nastavení použijte, pokud komunikace mezi uzly je potřeba šifrovat nebo chcete zadat název uzlu podle vašeho výběru. Názvy wwnn nelze upravit později.
3. Pokud se rozhodnete **Expresní instalace**
    1. Po úspěšné instalaci brány se zobrazí následující zpráva:

        ![Brána správy dat – úspěch Expresní instalace](media/data-factory-data-management-gateway-high-availability-scalability/express-setup-success.png)
    2. Spusťte Správce konfigurace správy dat pro bránu pomocí následujících [tyto pokyny](data-factory-data-management-gateway.md#configuration-manager). Zobrazí název brány, název uzlu, stav, atd.

        ![Brána správy dat – instalace byla úspěšná](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)
4. Pokud se rozhodnete **ruční instalaci**:
    1. Stáhněte si instalační balíček z webu Microsoft Download Center, spusťte ho, abyste bránu nainstalovat na svém počítači.
    2. Použití **ověřovací klíč** z **konfigurovat** stránka pro registraci brány.
    
        ![Brána správy dat – instalace byla úspěšná](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-authentication-key.png)
    3. V **nový uzel brány** stránky, můžete zadat vlastní **název** do uzlu brány. Ve výchozím nastavení název uzlu je stejný jako název počítače.    

        ![Brána správy dat – zadejte název](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-name.png)
    4. Na další stránce, můžete zvolit, jestli se má **povolit šifrování pro komunikaci mezi uzly**. Klikněte na tlačítko **přeskočit** můžete zakázat šifrování (výchozí).

        ![Brána správy dat – povolení šifrování](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-node-encryption.png)  
    
        > [!NOTE]
        > Změnit režim šifrování se podporuje jenom Pokud máte jednu bránu uzlu v logické brány. Chcete-li změnit režim šifrování, když brána má více uzlů, proveďte následující kroky: Odstraňte všechny uzly s výjimkou jednoho uzlu, změnit režim šifrování a pak přidejte uzly.
        > 
        > Zobrazit [požadavky na certifikát TLS/SSL](#tlsssl-certificate-requirements) najdete seznam požadavků pro používání certifikát TLS/SSL. 
    5. Po úspěšné instalaci brány, klikněte na spustit Správce konfigurace:
    
        ![Ruční instalace – spuštění configuration Manageru](media/data-factory-data-management-gateway-high-availability-scalability/manual-setup-launch-configuration-manager.png)   
    6. Zobrazí Správce konfigurace brány pro správu dat na uzlu (místní počítač Windows), který zobrazuje stav připojení, **název brány**, a **název uzlu**.  

        ![Brána správy dat – instalace byla úspěšná](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)

        > [!NOTE]
        > Pokud zřizujete brány na virtuální počítač Azure, můžete použít [tuto šablonu Azure Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/101-mutiple-vms-with-data-management-gateway). Tento skript vytvoří bránu logické, nastaví virtuální počítače s nainstalovaným softwarem brána správy dat a zaregistruje se logické brány. 
6. Na webu Azure portal, spusťte **brány** stránky: 
    1. Na objekt pro vytváření dat domovské stránce portálu, klikněte na tlačítko **propojené služby**.
    
        ![Domovská stránka objektu pro vytváření dat](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-home-page.png)
    2. Vyberte **brány** zobrazíte **brány** stránky:
    
        ![Domovská stránka objektu pro vytváření dat](media/data-factory-data-management-gateway-high-availability-scalability/linked-services-gateway.png)
    4. Zobrazí **brány** stránky:   

        ![Brána s jedním uzlem zobrazení](media/data-factory-data-management-gateway-high-availability-scalability/gateway-first-node-portal-view.png) 
7. Klikněte na tlačítko **přidat uzel** na panelu nástrojů můžete přidat uzel do logické brány. Pokud máte v úmyslu použít Expresní instalace, proveďte tento krok z místního počítače, které se přidají jako uzel k bráně. 

    ![Brána správy dat – přidání nabídky uzlu](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)
8. Kroky jsou podobné pro vytvoření prvního uzlu. Uživatelské rozhraní nástroje Configuration Manager vám umožňuje nastavit název uzlu, pokud zvolíte možnost Ruční instalace: 

    ![Configuration Manager – instalace druhá brána](media/data-factory-data-management-gateway-high-availability-scalability/install-second-gateway.png)
9. Po úspěšné instalaci brány v uzlu nástroje Configuration Manager zobrazí následující obrazovka:  

    ![Configuration Manager – druhá brána instalace úspěšná](media/data-factory-data-management-gateway-high-availability-scalability/second-gateway-installation-successful.png)
10. Pokud otevřete **brány** stránky na portálu se teď zobrazí dva uzly brány: 

    ![Brána se dvěma uzly na portálu](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)
11. Chcete-li odstranit uzel brány, klikněte na tlačítko **odstranit uzel** na panelu nástrojů vyberte uzel, který chcete odstranit a potom klikněte na tlačítko **odstranit** z panelu nástrojů. Tato akce odstraní vybraný uzel ze skupiny. Všimněte si, že tato akce neodinstaluje software brány správy dat z uzlu (místní počítač Windows). Použití **přidat nebo odebrat programy** v Ovládacích panelech odinstalujte bránu na místní. Když odinstalujete z uzlu brány, se automaticky odstraní na portálu.   

## <a name="upgrade-an-existing-gateway"></a>Upgrade existující brány
Můžete upgradovat stávající brány použít funkci vysoká dostupnost a škálovatelnost. Tato funkce funguje jenom s uzly, které mají brána správy dat verze > = 2.12.xxxx. Zobrazí verzi brána správy dat nainstalovaná na počítači ve **pomáhají** kartu na Data Management Gateway Configuration Manageru. 

1. Aktualizovat bránu na místní počítač na nejnovější verzi na základě stažením a spuštěná Instalační služba MSI instalační balíček z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Zobrazit [instalace](data-factory-data-management-gateway.md#installation) podrobné informace.  
2. Přejděte na web Azure Portal. Spusťte **stránce objektu pro vytváření dat** vaší datové továrny. Klikněte na dlaždici služby propojené ke spuštění **stránky propojené služby**. Vyberte bránu, spusťte **stránku brány**. Klikněte na tlačítko a povolit **funkce ve verzi Preview** jak je znázorněno na následujícím obrázku: 

    ![Brána správy dat – povolit funkci ve verzi preview](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-existing-gateway-enable-high-availability.png)   
2. Jakmile funkce ve verzi preview povolíte na portálu, zavřete všechny stránky. Znovu otevřít **stránku brány** zobrazíte nové uživatelské rozhraní (UI) ve verzi preview.
 
    ![Brána správy dat – úspěch funkci povolit ve verzi preview](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview-success.png)

    ![Brána správy dat – ve verzi preview uživatelského rozhraní](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview.png)

    > [!NOTE]
    > Během upgradu je třeba název prvního uzlu je název počítače. 
3. Teď přidejte uzel. V **brány** klikněte na **přidat uzel**.  

    ![Brána správy dat – přidání nabídky uzlu](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)

    Postupujte podle pokynů v předchozí části Nastavení uzlu. 

### <a name="installation-best-practices"></a>Osvědčené postupy instalace

- Konfigurovat schéma napájení na hostitelském počítači brány tak, aby počítač nepřejde do režimu spánku. Pokud hostitelský počítač přejde do režimu spánku, brána neodpovídá na požadavky na data.
- Zazálohujte si platnost certifikátu spojeného s bránou.
- Zajistěte, aby že všechny uzly jsou podobné konfigurace (doporučeno) ideální výkon. 
- Přidáte k zajištění vysoké dostupnosti alespoň dva uzly.  

### <a name="tlsssl-certificate-requirements"></a>Požadavky na certifikát TLS/SSL
Tady jsou požadavky na certifikát TLS/SSL, který se používá k zabezpečení komunikace mezi integrace uzly modulu runtime:

- Certifikát musí být veřejně důvěryhodné X509 certifikát v3. Doporučujeme používat certifikáty vydané veřejnou (třetí strany) certifikační autoritou (CA).
- Každý uzel integration runtime musí důvěřovat tomuto certifikátu, stejně jako klientský počítač, na kterém běží aplikace Správce pověření. 
> [!NOTE]
> Aplikace Správce pověření se používá při bezpečně nastavení přihlašovacích údajů z Průvodce kopírováním a webu Azure Portal. A to může být z libovolného počítače ve stejné síti jako místní aktivace / soukromé úložiště.
- Zástupné certifikáty jsou podporovány. Pokud je váš plně kvalifikovaný název domény **node1.domain.contoso.com**, můžete použít ***. domain.contoso.com** jako název subjektu certifikátu.
- Certifikátů SAN se nedoporučuje, protože se použijí jenom poslední položka alternativní názvy subjektů a všechny ostatní se bude ignorovat kvůli aktuálním omezením. Například máte certifikát SAN jehož SAN jsou **node1.domain.contoso.com** a **node2.domain.contoso.com**, tento certifikát můžete použít jenom na počítači, jehož plně kvalifikovaný název domény je **node2.domain.contoso.com**.
- Podporuje všechny klíče velikost podporovaná ve Windows serveru 2012 R2 pro certifikáty SSL.
- Certifikátů CNG pomocí klíče nejsou podporovány. Doesrted DoesDoes nepodporuje certifikáty, které používají klíči CNG.

#### <a name="faq-when-would-i-not-enable-this-encryption"></a>Nejčastější dotazy: Kdy by povolit šifrování?
Povolení šifrování můžete přidat určité náklady na infrastrukturu (vlastnící veřejného certifikátu), proto můžete přeskočit povolení šifrování v následující případy:
- Když se prostředí integration runtime běží v důvěryhodné síti, nebo v síti pomocí transparentního šifrování, jako jsou IP/s. Protože je tento komunikační kanál mezi pouze omezený ve vaší důvěryhodné síti, možná nebudete potřebovat další šifrování.
- Když se prostředí integration runtime neběží v produkčním prostředí. To může pomoct snížit náklady na certifikát TLS/SSL.


## <a name="monitor-a-multi-node-gateway"></a>Monitorování brány několika uzly
### <a name="multi-node-gateway-monitoring"></a>Monitorování několika uzly brány
Na webu Azure Portal uvidíte v reálném čase snímek využití prostředků (procesoru, paměti, network(in/out) atd.) na každém uzlu spolu s stavy uzlů brány. 

![Brána správy dat – monitorování více uzlů](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)

Můžete povolit **upřesňující nastavení** v **brány** stránku, abyste zobrazili Pokročilá metrika jako **sítě**(vstup/výstup), **roli & Stav přihlašovacích údajů**, což je užitečné při ladění problémů brány a **souběžných úloh** (systémem / Limit) který může být upravený / odpovídajícím způsobem změněné během optimalizace výkonu. Následující tabulka obsahuje popis sloupců **uzly brány** seznamu:  

Vlastnosti monitorování | Popis
:------------------ | :---------- 
Název | Název logické brány a uzly, které jsou přidružená k bráně.  
Status | Stav logické brány a uzly brány. Příklad: Online/Offline/Limited/atd. Informace o těchto stavů najdete v tématu [stav brány](#gateway-status) oddílu. 
Verze | Zobrazuje verzi logické brány a každý uzel brány. Verze logické brány je určena na základě verze Většina uzlů ve skupině. Pokud nejsou správně uzly s různými verzemi v nastavení logické brány pouze uzly se stejným číslem verze jako funkci logické brány. Ostatní jsou v režimu omezen a potřeba ručně aktualizovat (pouze v případě automatických aktualizací selže). 
Dostupná paměť | Dostupná paměť na uzel brány. Tato hodnota je snímek téměř v reálném čase. 
Využití procesoru | Využití procesoru uzlu brány. Tato hodnota je snímek téměř v reálném čase. 
Síť (vstup/výstup) | Využití brány uzlu sítě. Tato hodnota je snímek téměř v reálném čase. 
Souběžné úlohy (spuštění / Limit) | Počet úloh nebo úlohy spuštěné na každém uzlu. Tato hodnota je snímek téměř v reálném čase. Limit označuje maximální počet souběžných úloh pro každý uzel. Tato hodnota je definována v závislosti na velikosti počítačů. Můžete zvýšit limit vertikální navýšení kapacity provádění souběžné úlohy v pokročilých scénářích, kde procesoru / paměti / sítě je nevyužitých, ale aktivity se nestíhají dokončit. Tato možnost je také dostupné s jedním uzlem bránou (i když není povolená funkce, škálovatelnost a dostupnost). Další informace najdete v tématu [škálování aspekty](#scale-considerations) oddílu. 
Role | Existují dva typy rolí – dispečer a pracovního procesu. Všechny uzly jsou pracovních procesů, což znamená, že jsou všechny slouží ke spuštění úlohy. Existuje pouze jeden uzel dispečer, který slouží k vyžádání úlohy nebo úlohy ze služby cloud services a jejich vypravování do různých pracovních uzlů (včetně samotného). 

![Brána správy dat – rozšířené monitorování více uzlů](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-advanced.png)

### <a name="gateway-status"></a>Stav brány

Následující tabulka uvádí možné stavy **uzel brány**: 

Status  | Komentáře a scénáře
:------- | :------------------
Online | Uzel je připojený ke službě Data Factory.
Offline | Uzel je offline.
Upgrade | Uzel je automaticky aktualizuje.
Omezená | Z důvodu problému s připojením. Může být způsobeno problém 8050 portu HTTP, problém s připojením service bus nebo problémům synchronizace přihlašovacích údajů. 
Neaktivní | Uzel je v konfiguraci se liší od konfigurace dalších většinou uzlů.<br/><br/> Uzlem může být neaktivní, pokud se nemůže připojit k ostatním uzlům. 


Následující tabulka uvádí možné stavy **logické brány**. Stav brány, závisí na stavy uzlů brány. 

Status | Komentáře
:----- | :-------
Je nutné registrovat | Žádný uzel ještě není zaregistrované u této logické brány
Online | Uzly brány jsou online
Offline | Žádný uzel ve stavu online.
Omezená | Ne všechny uzly v této brány jsou v dobrém stavu. Tento stav se upozornění, že některý uzel může být mimo provoz. <br/><br/>Může být kvůli problémům synchronizace přihlašovacích údajů na dispečerský/pracovní uzel. 

### <a name="pipeline-activities-monitoring"></a>Kanál / monitorování aktivit
Na webu Azure portal poskytuje kanál monitorování prostředí s detailní uzel úroveň podrobností. Například ukazuje, které aktivity spustili na který uzel. Tyto informace můžou být užitečné porozumět problémům s výkonem na jednotlivé uzly, Řekněme, že z důvodu omezení využití sítě. 

![Brána správy dat – sledování pro kanály více uzlů](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-pipelines.png)

![Brána správy dat – podrobnosti o kanálu](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-pipeline-details.png)

## <a name="scale-considerations"></a>Důležité informace o škálování

### <a name="scale-out"></a>Horizontální navýšení kapacity
Když **dochází dostupná paměť** a **využití procesoru je Vysoká**, přidání nového uzlu pomáhá horizontální navýšení kapacity zatížení napříč počítači. Pokud aktivity se nedaří zálohovat z důvodu vypršení časového limitu nebo brány uzel je offline, pomůže, pokud chcete přidat uzel do brány.
 
### <a name="scale-up"></a>Vertikální navýšení kapacity
Pokud nejsou dostupné paměti a procesoru využívá dobře, ale nečinná kapacita je 0, by měla vertikálně navýšit kapacitu zvýšením počtu souběžných úloh, které můžou běžet na uzlu. Můžete také vertikálně navýšit kapacitu, když aktivity jsou vypršení časového limitu, protože brána je přetížena. Jak je znázorněno na následujícím obrázku, můžete zvýšit maximální kapacita pro uzel. Doporučujeme začít s možnost zdvojnásobení.  

![Brána správy dat – důležité informace o škálování](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-scale-considerations.png)


## <a name="known-issuesbreaking-changes"></a>Známé problémy a nejnovější změny

- V současné době může mít až čtyři uzly fyzická Brána pro jeden logický brány. Pokud potřebujete více než čtyři uzly z důvodů výkonu, odešlete e-mail na [ DMGHelp@microsoft.com ](mailto:DMGHelp@microsoft.com).
- Nelze znovu zaregistrujte uzel brány s ověřovacím klíčem z jiné logické brány přejděte z aktuální logické brány. Chcete-li znovu zaregistrovat, odinstalujte bránu z uzlu, nainstalujte bránu znovu a zaregistrovat ji pomocí ověřovacího klíče pro jiné logické brány. 
- Pokud proxy server HTTP je potřeba pro všechny uzly brány, nastavení proxy serveru v diahost.exe.config a diawp.exe.config a ujistěte se, že všechny uzly mají stejnou diahost.exe.config a diawip.exe.config pomocí Správce serveru. Zobrazit [konfigurace nastavení proxy serveru](data-factory-data-management-gateway.md#configure-proxy-server-settings) podrobné informace. 
- Chcete-li změnit režim šifrování komunikace mezi uzly ve Správci konfigurace brány, odstraňte všechny uzly na portálu s výjimkou jednoho. Pak přidejte uzly zpět po změně režimu šifrování.
- Použití oficiální certifikátu SSL, pokud se rozhodnete k šifrování komunikace mezi uzly kanálu. Certifikát podepsaný svým držitelem může způsobit problémy s připojením, protože stejný certifikát nemusí být důvěryhodný certifikační autoritu v seznamu na jiné počítače. 
- Uzel brány k logické brány nelze zaregistrovat, pokud verze uzlu je nižší než verze logické brány. Odstranit všechny uzly logické brány z portálu, aby zaregistrujete nižší verze node(downgrade) ho. Pokud odstraníte všechny uzly logické brány, ručně nainstalujte a zaregistrujte nových uzlů k této logické brány. Expresní instalace modulu není v tomto případě nepodporuje.
- Expresní instalace modulu nelze použít k instalaci uzlů do stávající logické bránu, která se pořád používá přihlašovací údaje do cloudu. Můžete zkontrolovat, kde jsou přihlašovací údaje uložené z Configuration Manageru brány na kartě nastavení.
- Expresní instalace modulu nelze použít k instalaci uzlů existující logické brány, který má povolené šifrování mezi uzly. Nastavení režimu šifrování zahrnuje ruční přidání certifikátů, je Expresní instalace žádné další možnost. 
- Pro kopírování souborů z místního prostředí, neměli byste používat \\localhost nebo C:\files už od místního hostitele nebo na místním disku možná není přístupný prostřednictvím všech uzlů. Místo toho použijte \\ServerName\files a zadejte umístění souborů.


## <a name="rolling-back-from-the-preview"></a>Vrácení zpět z verze preview 
Chcete-li vrátit zpět z verze preview, odstraňte všechny uzly, ale jeden uzel. Nezáleží na uzly, na kterých je odstranit, ale ujistěte se, že máte alespoň jeden uzel v logické brány. Můžete odstranit uzel odinstalací brány na počítači nebo pomocí webu Azure portal. Na webu Azure Portal v **služby Data Factory** klikněte na propojené služby, spusťte **propojené služby** stránky. Vyberte bránu, spusťte **brány** stránky. Na stránce brány uvidíte uzly přidružená k bráně. Stránce můžete odstranit uzel ze brána.
 
Po odstranění, klikněte na tlačítko **funkce ve verzi preview** ve stejné stránky Azure portal a zakázat funkce ve verzi preview. Resetování brány do jednoho uzlu GA (obecná dostupnost) brány.


## <a name="next-steps"></a>Další postup
Projděte si následující články:
- [Brána správy dat](data-factory-data-management-gateway.md) – poskytuje podrobný přehled o brány.
- [Přesun dat mezi místním a cloudovým úložištím dat](data-factory-move-data-between-onprem-and-cloud.md) – obsahuje návod s podrobnými pokyny pro použití brány se jeden uzel. 
