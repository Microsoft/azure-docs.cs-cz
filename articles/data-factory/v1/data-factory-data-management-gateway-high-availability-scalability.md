---
title: Vysoká dostupnost s bránou pro správu dat v Azure Data Factory
description: V tomto článku se dozvíte, jak můžete škálovat bránu pro správu dat tak, že přidáte další uzly a narostete jejich kapacitu zvýšením počtu souběžných úloh, které se můžou spouštět na uzlu.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: b8d05293359cff16bb6d8c9a629a1fbf68104365
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96003612"
---
# <a name="data-management-gateway---high-availability-and-scalability-preview"></a>Brána Správa dat – vysoká dostupnost a škálovatelnost (Preview)
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [Integration runtime v místním prostředí v](../create-self-hosted-integration-runtime.md). 


Tento článek vám pomůže nakonfigurovat řešení s vysokou dostupností a škálovatelností pomocí Správa dat brány nebo integrace.    

> [!NOTE]
> V tomto článku se předpokládá, že už jste obeznámení se základy Integration Runtime (dříve Správa dat bránu). Pokud ne, přečtěte si téma [Správa dat Gateway](data-factory-data-management-gateway.md).
> 
> **Tato funkce Preview je oficiálně podporovaná ve verzi Správa dat brány 2.12. xxxx. x a vyšší**. Ujistěte se prosím, že používáte verzi 2.12. xxxx. x nebo vyšší. Nejnovější verzi Správa dat brány si můžete stáhnout [tady](https://www.microsoft.com/download/details.aspx?id=39717).

## <a name="overview"></a>Přehled
Brány pro správu dat, které jsou nainstalované na několika místních počítačích s jednou logickou bránou, můžete přidružit na portál. Tyto počítače se nazývají **uzly**. K logické bráně je možné přidružit až **čtyři uzly** . Výhody, které mají více uzlů (místní počítače s nainstalovanou bránou) pro logickou bránu:  

- Zlepšení výkonu přesunu dat mezi místními a cloudovým úložištěm dat.  
- Pokud z nějakého důvodu dojde k výpadku některého z uzlů, k přesunu dat jsou stále k dispozici jiné uzly. 
- Pokud je třeba jeden z uzlů převést do režimu offline kvůli údržbě, další uzly jsou stále k dispozici pro přesun dat.

Můžete taky nakonfigurovat počet **souběžných úloh přesunu dat** , které se můžou spouštět na uzlu, abyste mohli škálovat možnosti přesouvání dat mezi místními a cloudovým úložištěm dat. 

Pomocí Azure Portal můžete monitorovat stav těchto uzlů, které vám pomůžou rozhodnout, jestli chcete uzel přidat nebo odebrat z logické brány. 

## <a name="architecture"></a>Architektura 
Následující diagram nabízí přehled architektury funkce Správa dat bránu pro škálovatelnost a dostupnost: 

![Brána Správa dat – vysoká dostupnost a škálovatelnost](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-high-availability-and-scalability.png)

**Logická brána** je brána, kterou přidáte do objektu pro vytváření dat v Azure Portal. Dříve jste mohli přidružit pouze jeden místní počítač s Windows s bránou Správa dat nainstalovanou s logickou bránou. Tento místní počítač brány se nazývá uzel. Nyní můžete k logické bráně přidružit až **čtyři fyzické uzly** . Logická brána s více uzly se nazývá brána s **více** uzly.  

Všechny tyto uzly jsou **aktivní**. Všechny můžou zpracovávat úlohy přesunu dat a přesouvat data mezi místními a cloudovým úložištěm dat. Jeden z uzlů působí jako dispečer i pracovní proces. Další uzly ve skupinách jsou pracovní uzly. **Dispečerský** uzel vyžádá úlohy přesunu dat nebo úlohy z cloudové služby a odesílá je do pracovních uzlů (včetně sebe samé). **Pracovní** uzel spouští úlohy přesunu dat pro přesun dat mezi místními a cloudovým úložištěm dat. Všechny uzly jsou pracovní procesy. Pouze jeden uzel může být Dispatch i Worker.    

Obvykle můžete začít s jedním uzlem a **škálovat** tak, aby bylo možné přidat další uzly, protože stávající uzly jsou zahlceny zatížením přesunu dat. Můžete také **škálovat** schopnost přesunu dat uzlu brány tím, že zvýšíte počet souběžných úloh, které se můžou spouštět na uzlu. Tato funkce je dostupná taky u brány s jedním uzlem (i když není povolená funkce škálovatelnosti a dostupnosti). 

Brána s více uzly udržuje přihlašovací údaje úložiště dat synchronizované napříč všemi uzly. Pokud dojde k potížím s připojením mezi uzly, přihlašovací údaje možná nebudou synchronizovány. Když nastavíte přihlašovací údaje pro místní úložiště dat, které používá bránu, uloží přihlašovací údaje na uzel dispečer nebo Worker. Uzel dispečera se synchronizuje s ostatními pracovními uzly. Tento proces se označuje jako **Synchronizace přihlašovacích údajů**. Komunikační kanál mezi uzly může být **zašifrovaný** veřejným certifikátem SSL/TLS. 

## <a name="set-up-a-multi-node-gateway"></a>Nastavení brány s více uzly
V této části se předpokládá, že jste prošli následujícími dvěma články nebo jste se seznámili se základními pojmy v těchto článcích: 

- [Správa dat Gateway](data-factory-data-management-gateway.md) – poskytuje podrobný přehled brány.
- [Přesun dat mezi místními a cloudovým úložištěm dat](data-factory-move-data-between-onprem-and-cloud.md) – obsahuje návod s podrobnými pokyny pro použití brány s jedním uzlem.  

> [!NOTE]
> Než nainstalujete bránu pro správu dat na místní počítač s Windows, přečtěte si část požadavky uvedené v [hlavním článku](data-factory-data-management-gateway.md#prerequisites).

1. V tomto [návodu](data-factory-move-data-between-onprem-and-cloud.md#create-gateway)při vytváření logické brány povolte funkci **škálovatelnosti & vysoké dostupnosti** . 

    ![Brána Správa dat – povolení vysoké dostupnosti a škálovatelnosti](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-enable-high-availability-scalability.png)
2. Na stránce **Konfigurace** použijte odkaz **Expresní instalace** nebo **Ruční instalace** k instalaci brány do prvního uzlu (místní počítač s Windows).

    ![Brána Správa dat – Expresní nebo ruční instalace](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-express-manual-setup.png)

    > [!NOTE]
    > Pokud použijete možnost Expresní instalace, komunikace mezi uzly se provádí bez šifrování. Název uzlu je stejný jako název počítače. Ruční instalace použijte v případě, že je potřeba šifrovat uzel uzlu, nebo chcete zadat název uzlu podle svého výběru. Názvy uzlů se nedají upravovat později.
3. Pokud zvolíte **expresní instalaci**
    1. Po úspěšné instalaci brány se zobrazí následující zpráva:

        ![Správa dat Gateway – Expresní instalace byla úspěšná](media/data-factory-data-management-gateway-high-availability-scalability/express-setup-success.png)
    2. Pomocí následujících [pokynů](data-factory-data-management-gateway.md#configuration-manager)spusťte pro bránu Správa dat Configuration Manager. Zobrazí se název brány, název uzlu, stav atd.

        ![Snímek obrazovky s informacemi o tom, kde vidíte název brány, název uzlu a stav](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)
4. Pokud zvolíte možnost **Ruční instalace**:
    1. Stáhněte instalační balíček z webu Microsoft Download Center, spusťte ho a nainstalujte na svém počítači bránu.
    2. K registraci brány použijte **ověřovací klíč** ze stránky **Konfigurace** .
    
        ![Snímek obrazovky, který ukazuje, kde použít ověřovací klíč](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-authentication-key.png)
    3. Na stránce **nový uzel brány** můžete zadat vlastní **název** uzlu brány. Ve výchozím nastavení je název uzlu stejný jako název počítače.    

        ![Správa dat Brána – zadejte název.](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-name.png)
    4. Na další stránce můžete zvolit, zda chcete **Povolit šifrování pro komunikaci** mezi uzly. Kliknutím na **Přeskočit** zakážete šifrování (výchozí).

        ![Brána Správa dat – povolení šifrování](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-node-encryption.png)  
    
        > [!NOTE]
        > Změna režimu šifrování se podporuje jenom v případě, že je v logické bráně jeden uzel brány. Chcete-li změnit režim šifrování, pokud má brána více uzlů, proveďte následující kroky: Odstraňte všechny uzly s výjimkou jednoho uzlu, změňte režim šifrování a pak uzly přidejte znovu.
        > 
        > Seznam požadavků na použití certifikátu TLS/SSL najdete v části [požadavky na certifikát TLS/SSL](#tlsssl-certificate-requirements) . 
    5. Po úspěšné instalaci brány klikněte na spustit Configuration Manager:
    
        ![Ruční instalace – spuštění nástroje Configuration Manager](media/data-factory-data-management-gateway-high-availability-scalability/manual-setup-launch-configuration-manager.png)   
    6. na uzlu (místní počítač s Windows) se zobrazí Správa dat Configuration Manager brány, která zobrazuje stav připojení, **název brány** a **název uzlu**.  

        ![Brána Správa dat – instalace byla úspěšná.](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)

        > [!NOTE]
        > Pokud zřizujete bránu na virtuálním počítači Azure, můžete použít [tuto šablonu Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-mutiple-vms-with-data-management-gateway). Tento skript vytvoří logickou bránu, nastaví virtuální počítače s nainstalovaným softwarem Správa dat brány a zaregistruje je pomocí logické brány. 
6. V Azure Portal spusťte stránku **brány** : 
    1. Na domovské stránce objektu pro vytváření dat na portálu klikněte na **propojené služby**.
    
        ![Snímek obrazovky, který zvýrazní dlaždici propojených služeb.](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-home-page.png)
    2. Vyberte **bránu** , aby se zobrazila stránka **brány** :
    
        ![Domovská stránka objektu pro vytváření dat](media/data-factory-data-management-gateway-high-availability-scalability/linked-services-gateway.png)
    4. Zobrazí se stránka **Brána** :   

        ![Brána s jedním zobrazením uzlů](media/data-factory-data-management-gateway-high-availability-scalability/gateway-first-node-portal-view.png) 
7. Kliknutím na **přidat uzel** na panelu nástrojů přidejte uzel do logické brány. Pokud plánujete použít expresní instalaci, udělejte tento krok z místního počítače, který se přidá jako uzel do brány. 

    ![Brána Správa dat – nabídka přidat uzel](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)
8. Postup je podobný jako při nastavení prvního uzlu. Configuration Manager uživatelské rozhraní umožňuje nastavit název uzlu, pokud zvolíte možnost Ruční instalace: 

    ![Configuration Manager – instalace druhé brány](media/data-factory-data-management-gateway-high-availability-scalability/install-second-gateway.png)
9. Po úspěšné instalaci brány na uzlu se v nástroji Configuration Manager zobrazí následující obrazovka:  

    ![Configuration Manager – úspěšná instalace druhé brány](media/data-factory-data-management-gateway-high-availability-scalability/second-gateway-installation-successful.png)
10. Pokud otevřete stránku **brány** na portálu, zobrazí se teď dva uzly brány: 

    ![Brána se dvěma uzly na portálu](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)
11. Pokud chcete odstranit uzel brány, klikněte na panelu nástrojů na **Odstranit uzel** , vyberte uzel, který chcete odstranit, a potom na panelu nástrojů klikněte na **Odstranit** . Tato akce odstraní vybraný uzel ze skupiny. Všimněte si, že tato akce neodinstaluje software brány pro správu dat z uzlu (místní počítač s Windows). Bránu odinstalujte pomocí panelu **Přidat nebo odebrat programy** v Ovládacích panelech v místním prostředí. Když bránu odinstalujete z uzlu, automaticky se odstraní na portálu.   

## <a name="upgrade-an-existing-gateway"></a>Upgrade existující brány
Existující bránu můžete upgradovat tak, aby používala funkci vysoké dostupnosti a škálovatelnosti. Tato funkce funguje jenom s uzly, které mají bránu pro správu dat verze >= 2.12. xxxx. Verzi brány pro správu dat nainstalovanou na počítači můžete zobrazit na kartě **Help** Configuration Manager Správa dat brány. 

1. Pomocí stažení a spuštění instalačního balíčku MSI z [webu Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717)aktualizujte bránu na místním počítači na nejnovější verzi. Podrobnosti najdete v části věnované [instalaci](data-factory-data-management-gateway.md#installation) .  
2. Přejděte na Azure Portal. Spusťte **Data Factory stránku** pro datovou továrnu. Kliknutím na dlaždici propojené služby otevřete **stránku propojené služby**. Vyberte bránu, na které chcete spustit **stránku brány**. Klikněte na možnost povolit **funkci ve verzi Preview** , jak je znázorněno na následujícím obrázku: 

    ![Funkce pro povolení verze Preview služby Správa dat Gateway](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-existing-gateway-enable-high-availability.png)   
2. Po povolení funkce Preview na portálu zavřete všechny stránky. Znovu otevřete **stránku brány** , abyste viděli nové uživatelské rozhraní verze Preview (UI).
 
    ![Správa dat brána – povolit funkci verze Preview úspěšné](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview-success.png)

    ![ROZHRANÍ Správa dat Gateway – Preview](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview.png)

    > [!NOTE]
    > Během upgradu název prvního uzlu je název počítače. 
3. Nyní přidejte uzel. Na stránce **Brána** klikněte na **přidat uzel**.  

    ![Brána Správa dat – nabídka přidat uzel](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)

    Postupujte podle pokynů v předchozí části a nastavte uzel. 

### <a name="installation-best-practices"></a>Osvědčené postupy instalace

- Nakonfigurujte schéma napájení na hostitelském počítači pro bránu tak, aby se počítač nev režimu hibernace. Pokud se hostitelský počítač přepne do režimu hibernace, brána nereaguje na požadavky na data.
- Zálohujte certifikát přidružený k bráně.
- Ujistěte se, že všechny uzly mají podobnou konfiguraci (doporučeno) pro ideální výkon. 
- Přidejte alespoň dva uzly, abyste zajistili vysokou dostupnost.  

### <a name="tlsssl-certificate-requirements"></a>Požadavky na certifikát TLS/SSL
Tady jsou požadavky na certifikát TLS/SSL, který se používá k zabezpečení komunikace mezi uzly Integration Runtime:

- Certifikát musí být veřejně důvěryhodný certifikát x509 v3. Doporučujeme používat certifikáty vydávané veřejnou certifikační autoritou (CA) třetí strany.
- Každý uzel Integration runtime musí důvěřovat tomuto certifikátu a také klientskému počítači, na kterém je spuštěná aplikace Správce přihlašovacích údajů. 
  > [!NOTE]
  > Aplikace Správce přihlašovacích údajů se používá při bezpečném nastavení přihlašovacích údajů z Průvodce kopírováním nebo na webu Azure Portal. A dá se aktivovat z libovolného počítače ve stejné síti jako místní nebo privátní úložiště dat.
- Jsou podporovány zástupné certifikáty karet. Pokud je název plně kvalifikovaného názvu domény **node1.domain.contoso.com**, můžete jako název subjektu certifikátu použít **_. domain.contoso.com_* .
- Certifikáty SAN se nedoporučují, protože se použijí jenom poslední položka alternativních názvů subjektu a všechny ostatní budou v důsledku současného omezení ignorovány. Například máte certifikát SAN, jehož síť SAN je **node1.domain.contoso.com** a **node2.domain.contoso.com**, můžete tento certifikát použít jenom na počítači, jehož plně kvalifikovaný název domény je **node2.domain.contoso.com**.
- Podporuje jakoukoli velikost klíče podporovanou systémem Windows Server 2012 R2 pro certifikáty TLS/SSL.
- Certifikát používající klíče CNG není podporován.

#### <a name="faq-when-would-i-not-enable-this-encryption"></a>Nejčastější dotazy: kdy by nebylo možné toto šifrování povolit?
Povolení šifrování může přidat určité náklady do vaší infrastruktury (vlastnící veřejný certifikát), takže můžete v následujících případech přeskočit povolení šifrování:
- Když je prostředí Integration runtime spuštěné v důvěryhodné síti nebo síť s transparentním šifrováním, jako je IP/s. Vzhledem k tomu, že tato komunikace kanálu je omezená jenom v rámci důvěryhodné sítě, možná nebudete potřebovat další šifrování.
- V případě, že modul runtime integrace není spuštěn v produkčním prostředí. To může přispět k omezení nákladů na certifikát TLS/SSL.


## <a name="monitor-a-multi-node-gateway"></a>Monitorování brány s více uzly
### <a name="multi-node-gateway-monitoring"></a>Monitorování brány s více uzly
V Azure Portal můžete zobrazit snímek téměř reálného času využití prostředků (CPU, paměť, síť (v/v) atd.) na každém uzlu spolu se stavy uzlů brány. 

![Brána Správa dat – monitorování více uzlů](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)

Můžete povolit **Rozšířená nastavení** na stránce **brány** a zobrazit tak pokročilé metriky, jako je například **síť**(za provozu), **role & přihlašovací údaje**, což je užitečné při ladění potíží s bránou a **souběžné úlohy** (spuštěné/omezení), které se dají upravovat nebo měnit, a to během optimalizace výkonu. Následující tabulka uvádí popisy sloupců v seznamu **uzly brány** :  

Vlastnost monitorování | Popis
:------------------ | :---------- 
Název | Název logické brány a uzlů přidružených k bráně  
Status | Stav logické brány a uzlů brány. Příklad: online/offline/omezený/atd. Informace o těchto stavech najdete v části [stav brány](#gateway-status) . 
Verze | Zobrazuje verzi logické brány a všech uzlů brány. Verze logické brány je určena na základě verze většiny uzlů ve skupině. Pokud v instalaci logické brány existují uzly s různými verzemi, budou správně fungovat pouze uzly se stejným číslem verze jako logická brána. Ostatní jsou v omezeném režimu a je potřeba je ručně aktualizovat (jenom v případě, že se automatická aktualizace nezdařila). 
Dostupná paměť | Dostupná paměť v uzlu brány Tato hodnota je snímkem téměř v reálném čase. 
Využití procesoru | Využití procesoru uzlu brány Tato hodnota je snímkem téměř v reálném čase. 
Sítě (za sekundu) | Využití sítě uzlu brány. Tato hodnota je snímkem téměř v reálném čase. 
Souběžné úlohy (spuštěné/omezení) | Počet úloh nebo úloh, které jsou spuštěny na jednotlivých uzlech. Tato hodnota je snímkem téměř v reálném čase. Limit označuje maximální počet souběžných úloh pro každý uzel. Tato hodnota je definovaná na základě velikosti počítače. Limit můžete zvýšit tak, aby se v pokročilých scénářích zvýšilo zatížení souběžných úloh, kde je procesor/paměť/síť využívána, ale aktivity čekají na vypršení časového limitu. Tato funkce je dostupná taky u brány s jedním uzlem (i když není povolená funkce škálovatelnosti a dostupnosti). Další informace najdete v části věnované [hlediskům škálování](#scale-considerations) . 
Role | Existují dva typy rolí – dispečer a pracovní proces. Všechny uzly jsou pracovní procesy, což znamená, že je můžete použít ke spouštění úloh. K dispozici je jen jeden uzel dispečera, který se používá k vyžádání úkolů nebo úloh z cloudových služeb a jejich odeslání do různých pracovních uzlů (včetně sebe samé). 

![Brána Správa dat – pokročilé monitorování více uzlů](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-advanced.png)

### <a name="gateway-status"></a>Stav brány

Následující tabulka uvádí možné stavy **uzlu brány**: 

Status  | Komentáře a scénáře
:------- | :------------------
Online | Uzel je připojený ke službě Data Factory.
Offline | Uzel je offline.
Inovován | Uzel se automaticky aktualizuje.
Omezeně | Kvůli problému s připojením. Důvodem může být problém s portem HTTP 8050, potížím s připojením k Service Bus nebo problémy s synchronizací přihlašovacích údajů. 
Inactive | Uzel je v konfiguraci odlišnou od konfigurace jiných majoritní uzlů.<br/><br/> Uzel může být neaktivní, pokud se nemůže připojit k jiným uzlům. 


Následující tabulka uvádí možné stavy **logické brány**. Stav brány závisí na stavech uzlů brány. 

Status | Komentáře
:----- | :-------
Vyžaduje registraci | Pro tuto logickou bránu ještě není zaregistrovaný žádný uzel.
Online | Uzly brány jsou online.
Offline | Žádný uzel ve stavu online.
Omezeně | Ne všechny uzly v této bráně jsou v dobrém stavu. Tento stav je upozorněním, že některý uzel může být mimo provoz. <br/><br/>Důvodem může být problém synchronizace přihlašovacích údajů u dispečera nebo pracovního uzlu. 

### <a name="pipeline-activities-monitoring"></a>Monitorování kanálu/aktivit
Azure Portal poskytuje prostředí monitorování kanálu s podrobnými podrobnostmi na úrovni uzlu. Zobrazuje například aktivity, které byly spuštěny na kterých uzlech. Tyto informace mohou být užitečné při porozumění problémům s výkonem na konkrétním uzlu, a to kvůli omezení sítě. 

![Brána Správa dat – monitorování více uzlů pro kanály](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-pipelines.png)

![Správa dat brány – podrobnosti kanálu](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-pipeline-details.png)

## <a name="scale-considerations"></a>Požadavky na škálování

### <a name="scale-out"></a>Horizontální navýšení kapacity
Když **je dostupná paměť nízká** a **využití CPU je vysoké**, přidání nového uzlu pomáhá škálovat zatížení napříč počítači. Pokud dojde k selhání aktivit z důvodu vypršení časového limitu nebo uzlu brány v režimu offline, pomůže vám to, když do brány přidáte uzel.
 
### <a name="scale-up"></a>Vertikální navýšení kapacity
Pokud není dostupná paměť a procesor dobře využité, ale kapacita nečinnosti je 0, měli byste škálovat kapacitu zvýšením počtu souběžných úloh, které se můžou spouštět na uzlu. Možná budete chtít škálovat i v případě, že aktivity čekají na vypršení časového limitu, protože brána je přetížená. Jak je znázorněno na následujícím obrázku, můžete zvýšit maximální kapacitu uzlu. Doporučujeme, abyste na začátku začali.  

![Správa dat požadavky na škálování brány](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-scale-considerations.png)


## <a name="known-issuesbreaking-changes"></a>Známé problémy/zásadní změny

- V současné době můžete mít až čtyři uzly fyzické brány pro jednu logickou bránu. Pokud potřebujete více než čtyři uzly z důvodu výkonu, odešlete e-mail na adresu [DMGHelp@microsoft.com](mailto:DMGHelp@microsoft.com) .
- Nemůžete znovu zaregistrovat uzel brány s ověřovacím klíčem z jiné logické brány pro přepnutí z aktuální logické brány. Pokud se chcete znovu zaregistrovat, odinstalujte bránu z uzlu, znovu nainstalujte bránu a zaregistrujte ji pomocí ověřovacího klíče pro druhou logickou bránu. 
- Pokud je pro všechny uzly brány vyžadován proxy server HTTP, nastavte proxy server v diahost.exe.config a diawp.exe.config a pomocí Správce serveru zajistěte, aby všechny uzly měly stejné diahost.exe.config a diawip.exe.config. Podrobnosti najdete v části [Konfigurace nastavení proxy serveru](data-factory-data-management-gateway.md#configure-proxy-server-settings) . 
- Pokud chcete změnit režim šifrování pro komunikaci mezi uzly v Configuration Manager brány, odstraňte všechny uzly na portálu s výjimkou jednoho. Po změně režimu šifrování pak přidejte uzly zpátky.
- Pokud se rozhodnete šifrovat komunikační kanál mezi uzly, použijte oficiální certifikát TLS. Certifikát podepsaný svým držitelem může způsobit problémy s připojením, protože stejný certifikát nemusí být důvěryhodný v seznamu certifikační autority na jiných počítačích. 
- Uzel brány nejde zaregistrovat k logické bráně, pokud je verze uzlu nižší než verze logické brány. Odstraňte všechny uzly logické brány z portálu, abyste mohli registrovat nižší verzi uzlu (downgrade). Pokud odstraníte všechny uzly logické brány, ručně nainstalujte a zaregistrujte nové uzly na tuto logickou bránu. Expresní instalace není v tomto případě podporována.
- Expresní instalaci nemůžete použít k instalaci uzlů do existující logické brány, která pořád používá cloudové přihlašovací údaje. Můžete kontrolovat, kde jsou přihlašovací údaje uložené, Configuration Manager brány na kartě nastavení.
- Expresní instalaci nelze použít k instalaci uzlů do existující logické brány s povoleným šifrováním mezi uzly. Protože nastavení režimu šifrování zahrnuje Ruční přidávání certifikátů, Expresní instalace není další možností. 
- V případě kopírování souborů z místního prostředí byste už neměli používat \\ localhost ani C:\files, protože localhost nebo místní disk nemusí být přístupné přes všechny uzly. Místo toho použijte \\ ServerName\files k určení umístění Files.


## <a name="rolling-back-from-the-preview"></a>Vrácení zpět z verze Preview 
Chcete-li se vrátit z verze Preview, odstraňte všechny uzly, ale jeden uzel. Nezáleží na tom, které uzly jste odstranili, ale ujistěte se, že máte v logické bráně aspoň jeden uzel. Uzel můžete odstranit buď odinstalováním brány na počítači, nebo pomocí Azure Portal. V Azure Portal na stránce **Data Factory** kliknutím na propojené služby otevřete stránku **propojené služby** . Vyberte bránu, na které chcete spustit stránku **brány** . Na stránce Brána uvidíte uzly přidružené k bráně. Stránka umožňuje odstranit uzel z brány.
 
Po odstranění klikněte na **funkce verze Preview** na stejné Azure Portal stránce a zakažte funkci verze Preview. Svou bránu jste obnovili na jednu bránu pro uzel GA (všeobecně dostupná).


## <a name="next-steps"></a>Další kroky
Přečtěte si následující články:
- [Správa dat Gateway](data-factory-data-management-gateway.md) – poskytuje podrobný přehled brány.
- [Přesun dat mezi místními a cloudovým úložištěm dat](data-factory-move-data-between-onprem-and-cloud.md) – obsahuje návod s podrobnými pokyny pro použití brány s jedním uzlem. 
