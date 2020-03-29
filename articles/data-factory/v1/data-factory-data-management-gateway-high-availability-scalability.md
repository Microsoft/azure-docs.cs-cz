---
title: Vysoká dostupnost s bránou pro správu dat v Azure Data Factory
description: Tento článek vysvětluje, jak můžete škálovat bránu pro správu dat přidáním dalších uzlů a škálování na kapacitu zvýšením počtu souběžných úloh, které lze spustit na uzlu.
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
ms.openlocfilehash: 4ee89f4bba70bb5e81eef21247d556f65a2a1f16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065193"
---
# <a name="data-management-gateway---high-availability-and-scalability-preview"></a>Brána pro správu dat – vysoká dostupnost a škálovatelnost (preview)
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [runtime integrace s vlastním hostitelem v .](../create-self-hosted-integration-runtime.md) 


Tento článek vám pomůže nakonfigurovat řešení vysoké dostupnosti a škálovatelnosti pomocí brány pro správu dat nebo integrace.    

> [!NOTE]
> Tento článek předpokládá, že jste již obeznámeni se základy integrace Runtime (Dřívější brána pro správu dat). Pokud tomu tak není, přečtěte [si informace o bráně pro správu dat](data-factory-data-management-gateway.md).
> 
> **Tato funkce náhledu je oficiálně podporována v bráně pro správu dat verze 2.12.xxxx.x a vyšší**. Ujistěte se, že používáte verzi 2.12.xxxx.x nebo vyšší. Stáhněte si nejnovější verzi Brány pro správu [dat zde](https://www.microsoft.com/download/details.aspx?id=39717).

## <a name="overview"></a>Přehled
Brány pro správu dat, které jsou nainstalovány na více místních počítačích, můžete z portálu přidružit k jedné logické bráně. Tyto počítače se nazývají **uzly**. K logické bráně můžete mít přidružené až **čtyři uzly.** Výhody, které mají více uzlů (místní počítače s nainstalovanou bránou) pro logickou bránu jsou:  

- Zlepšete výkon pohybu dat mezi místními a cloudovými úložišti dat.  
- Pokud jeden z uzlů přejde z nějakého důvodu, ostatní uzly jsou stále k dispozici pro přesunutí dat. 
- Pokud jeden z uzlů je třeba převést do režimu offline pro údržbu, ostatní uzly jsou stále k dispozici pro přesunutí dat.

Můžete také nakonfigurovat počet **souběžných úloh přesunu dat,** které lze spustit v uzlu, a vertikálně navýšit kapacitu možností přesouvání dat mezi místními a cloudovými úložišti dat. 

Pomocí portálu Azure můžete sledovat stav těchto uzlů, což vám pomůže rozhodnout, jestli chcete přidat nebo odebrat uzel z logické brány. 

## <a name="architecture"></a>Architektura 
Následující diagram poskytuje přehled architektury škálovatelnosti a dostupnosti brány pro správu dat: 

![Brána pro správu dat – vysoká dostupnost a škálovatelnost](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-high-availability-and-scalability.png)

**Logická brána** je brána, kterou přidáte do datové továrny na webu Azure Portal. Dříve bylo možné přidružit pouze jeden místní počítač se systémem Windows s bránou pro správu dat nainstalovanou s logickou bránou. Tento místní počítač brány se nazývá uzel. Nyní můžete přidružit až **čtyři fyzické uzly** s logickou bránou. Logická brána s více uzly se nazývá **brána s více uzly**.  

Všechny tyto uzly jsou **aktivní**. Všechny mohou zpracovávat úlohy přesunu dat k přesunu dat mezi místními a cloudovými úložišti dat. Jeden z uzlů působí jako dispečer i pracovník. Ostatní uzly ve skupinách jsou pracovní uzly. **Dispečerský** uzel získává úlohy/úlohy přesunu dat z cloudové služby a odesílá je do pracovních uzlů (včetně samotných). **Pracovní uzel** provádí úlohy přesunu dat k přesunu dat mezi místními a cloudovými úložišti dat. Všechny uzly jsou pracovníci. Pouze jeden uzel může být odeslání a pracovník.    

Obvykle můžete začít s jedním uzlem a **horizontálním navýšením kapacity** pro přidání dalších uzlů, protože existující uzly jsou zahlceny zatížením přesunu dat. Můžete také **vertikálně zvýšit** kapacitu funkce přesunu dat uzlu brány zvýšením počtu souběžných úloh, které mohou být v uzlu spuštěny. Tato funkce je také k dispozici s bránou s jedním uzlem (i když není povolena funkce škálovatelnosti a dostupnosti). 

Brána s více uzly udržuje přihlašovací údaje úložiště dat synchronizované ve všech uzlech. Pokud je problém s připojením mezi uzly, pověření mohou být synchronizována. Když nastavíte pověření pro místní úložiště dat, které používá bránu, uloží pověření na uzlu dispečera/pracovníka. Uzel dispečera se synchronizuje s ostatními pracovními uzly. Tento proces se označuje jako **synchronizace pověření**. Komunikační kanál mezi uzly může být **šifrován** veřejným certifikátem SSL/TLS. 

## <a name="set-up-a-multi-node-gateway"></a>Nastavení brány s více uzly
Tato část předpokládá, že jste prošli následující dva články nebo obeznámeni s koncepty v těchto článcích: 

- [Brána pro správu dat](data-factory-data-management-gateway.md) - poskytuje podrobný přehled o bráně.
- [Přesun dat mezi místními a cloudovými úložišti dat](data-factory-move-data-between-onprem-and-cloud.md) – obsahuje návod s podrobnými pokyny pro použití brány s jedním uzly.  

> [!NOTE]
> Před instalací brány pro správu dat do místního počítače se systémem Windows najdete požadavky uvedené v [hlavním článku](data-factory-data-management-gateway.md#prerequisites).

1. V [návodu](data-factory-move-data-between-onprem-and-cloud.md#create-gateway)můžete při vytváření logické brány povolit funkci **Vysoká dostupnost & škálovatelnost.** 

    ![Brána pro správu dat – umožňuje vysokou dostupnost a škálovatelnost](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-enable-high-availability-scalability.png)
2. Na stránce **Konfigurace** použijte k instalaci brány do prvního uzlu (místního počítače se systémem Windows) pomocí odkazu **Expresní instalace** nebo **Ruční instalace.**

    ![Brána pro správu dat - expresní nebo ruční nastavení](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-express-manual-setup.png)

    > [!NOTE]
    > Pokud použijete možnost expresní nastavení, komunikace mezi uzlinami se provádí bez šifrování. Název uzlu je stejný jako název počítače. Ruční nastavení použijte, pokud je třeba zašifrovat komunikaci uzlu nebo chcete zadat název uzlu podle vašeho výběru. Názvy uzlů nelze později upravovat.
3. Pokud zvolíte **expresní nastavení**
    1. Po úspěšné instalaci brány se zobrazí následující zpráva:

        ![Brána pro správu dat – expresní úspěšnost nastavení](media/data-factory-data-management-gateway-high-availability-scalability/express-setup-success.png)
    2. Spusťte správce konfigurace správy dat pro bránu podle [následujících pokynů](data-factory-data-management-gateway.md#configuration-manager). Zobrazí se název brány, název uzlu, stav atd.

        ![Brána pro správu dat - instalace úspěšná](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)
4. Pokud zvolíte **ruční nastavení**:
    1. Stáhněte si instalační balíček ze služby Stažení softwaru společnosti Microsoft a spusťte jej a nainstalujte bránu do počítače.
    2. K registraci brány použijte **ověřovací klíč** ze stránky **Konfigurovat.**
    
        ![Brána pro správu dat - instalace úspěšná](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-authentication-key.png)
    3. Na stránce **Uzla Nová brána** můžete uzlovat vlastní **název.** Ve výchozím nastavení je název uzlu stejný jako název počítače.    

        ![Brána pro správu dat - zadat název](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-name.png)
    4. Na další stránce můžete zvolit, zda chcete **povolit šifrování pro komunikaci mezi uzlami**. Chcete-li zakázat šifrování (výchozí), klepněte na **tlačítko Přeskočit.**

        ![Brána pro správu dat – povolit šifrování](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-node-encryption.png)  
    
        > [!NOTE]
        > Změna režimu šifrování je podporována pouze v případě, že máte jeden uzel brány v logické bráně. Chcete-li změnit režim šifrování, pokud má brána více uzlů, proveďte následující kroky: odstraňte všechny uzly kromě jednoho uzlu, změňte režim šifrování a pak je znovu přidejte.
        > 
        > Seznam požadavků [na certifikátts/SSL](#tlsssl-certificate-requirements) naleznete v části Požadavky na certifikát TLS/SSL, kde naleznete seznam požadavků na použití certifikátu TLS/SSL. 
    5. Po úspěšné instalaci brány klikněte na Spustit Nástroj pro konfiguraci:
    
        ![Ruční nastavení - správce konfigurace spuštění](media/data-factory-data-management-gateway-high-availability-scalability/manual-setup-launch-configuration-manager.png)   
    6. Na uzlu (místní počítač se systémem Windows) se zobrazí správce konfigurace brány správy dat, který zobrazuje stav připojení, **název brány**a **název uzlu**.  

        ![Brána pro správu dat - instalace úspěšná](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)

        > [!NOTE]
        > Pokud zřáváte bránu na virtuálním počítači Azure, můžete použít [tuto šablonu Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-mutiple-vms-with-data-management-gateway). Tento skript vytvoří logickou bránu, nastaví virtuální počítače s nainstalovaným softwarem Brány pro správu dat a zaregistruje je pomocí logické brány. 
6. Na Webu Azure Portal spusťte stránku **Brána:** 
    1. Na domovské stránce datové továrny na portálu klikněte na **Linked Services**.
    
        ![Domovská stránka objektu pro vytváření dat](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-home-page.png)
    2. vyberte **bránu,** chcete-li zobrazit stránku **Brána:**
    
        ![Domovská stránka objektu pro vytváření dat](media/data-factory-data-management-gateway-high-availability-scalability/linked-services-gateway.png)
    4. Zobrazí se stránka **Brána:**   

        ![Brána se zobrazením jednoho uzlu](media/data-factory-data-management-gateway-high-availability-scalability/gateway-first-node-portal-view.png) 
7. Kliknutím na **Přidat uzel** na panelu nástrojů přidejte uzel do logické brány. Pokud plánujete použít expresní nastavení, proveďte tento krok z místního počítače, který bude přidán jako uzel do brány. 

    ![Brána pro správu dat - nabídka přidat uzel](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)
8. Kroky jsou podobné nastavení prvního uzlu. Uživatelské nastavení nástroje Configuration Manager umožňuje nastavit název uzlu, pokud zvolíte možnost ruční instalace: 

    ![Configuration Manager - instalace druhé brány](media/data-factory-data-management-gateway-high-availability-scalability/install-second-gateway.png)
9. Po úspěšné instalaci brány do uzlu zobrazí nástroj Configuration Manager následující obrazovku:  

    ![Configuration Manager - instalace druhé brány úspěšná](media/data-factory-data-management-gateway-high-availability-scalability/second-gateway-installation-successful.png)
10. Pokud otevřete stránku **Brána** na portálu, zobrazí se nyní dva uzly brány: 

    ![Brána se dvěma uzly na portálu](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)
11. Pokud chcete odstranit uzel brány, klikněte na panelu nástrojů na **Odstranit uzel,** vyberte uzel, který chcete odstranit, a potom na Odstranit z panelu nástrojů klikněte na **Odstranit.** Tato akce odstraní vybraný uzel ze skupiny. Všimněte si, že tato akce neodinstaluje software brány pro správu dat z uzlu (místní hopočítače se systémem Windows). Odinstalace brány slouží k odinstalaci brány pomocí ovládacího panelu **Přidat nebo odebrat** programy v místním prostředí. Když odinstalujete bránu z uzlu, automaticky se odstraní na portálu.   

## <a name="upgrade-an-existing-gateway"></a>Upgrade existující brány
Můžete upgradovat existující bránu, abyste mohli používat funkci vysoké dostupnosti a škálovatelnosti. Tato funkce funguje pouze s uzly, které mají bránu pro správu dat verze >= 2.12.xxxx. Verzi brány pro správu dat nainstalovanou v počítači najdete na kartě **Nápověda** správce konfigurace brány brány pro správu dat. 

1. Aktualizujte bránu v místním počítači na nejnovější verzi stažením a spuštěním instalačního balíčku MSI ze [služby Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=39717). Podrobnosti naleznete v části [instalace.](data-factory-data-management-gateway.md#installation)  
2. Přejděte na Azure Portal. Spusťte **stránku Data Factory** pro vaši datovou továrnu. Kliknutím na dlaždici Propojené služby spusťte **stránku propojených služeb**. Vyberte bránu pro spuštění **stránky brány**. Klikněte a povolte **funkci náhledu,** jak je znázorněno na následujícím obrázku: 

    ![Brána pro správu dat – povolit funkci náhledu](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-existing-gateway-enable-high-availability.png)   
2. Jakmile je funkce náhledu na portálu povolena, zavřete všechny stránky. Znovu otevřete **stránku brány** a zobrazte nové uživatelské rozhraní náhledu.
 
    ![Brána pro správu dat – povolit úspěch funkce náhledu](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview-success.png)

    ![Brána pro správu dat – náhled ui](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview.png)

    > [!NOTE]
    > Během upgradu je název prvního uzlu název počítače. 
3. Nyní přidejte uzel. Na stránce **Brána** klikněte na **Přidat uzel**.  

    ![Brána pro správu dat - nabídka přidat uzel](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)

    Podle pokynů z předchozí části nastavte uzel. 

### <a name="installation-best-practices"></a>Osvědčené postupy instalace

- Nakonfigurujte plán napájení na hostitelském počítači pro bránu tak, aby počítač nebyl přepzen do režimu spánku. Pokud hostitelský počítač přejde do režimu spánku, brána nereaguje na požadavky na data.
- Záloha certifikát přidružený k bráně.
- Ujistěte se, že všechny uzly mají podobnou konfiguraci (doporučeno) pro ideální výkon. 
- Přidejte alespoň dva uzly, abyste zajistili vysokou dostupnost.  

### <a name="tlsssl-certificate-requirements"></a>Požadavky na certifikát TLS/SSL
Zde jsou požadavky na certifikát TLS/SSL, který se používá k zabezpečení komunikace mezi integračními runtime uzly:

- Certifikát musí být veřejně důvěryhodný certifikát X509 v3. Doporučujeme používat certifikáty vydané veřejným certifikačním úřadem (třetí strany).
- Každý uzel runtime integrace musí důvěřovat tomuto certifikátu, stejně jako klientský počítač, ve kterých je spuštěna aplikace správce pověření. 
  > [!NOTE]
  > Aplikace správce přihlašovacích údajů se používá při bezpečném nastavení přihlašovacích údajů z Průvodce kopírováním nebo portálu Azure Portal. A to může být aktivována z libovolného počítače ve stejné síti jako místní / soukromé úložiště dat.
- Certifikáty s divokou kartou jsou podporovány. Pokud je název vašeho hlavního názvu kontinuální označení **node1.domain.contoso.com**, můžete jako název subjektu certifikátu použít ***.domain.contoso.com.**
- Certifikáty SAN se nedoporučují, protože bude použita pouze poslední položka alternativních názvů předmětů a všechny ostatní budou ignorovány z důvodu aktuálního omezení. Například Máte certifikát SÍTĚ SAN, jehož sítě SAN jsou **node1.domain.contoso.com** a **node2.domain.contoso.com**, můžete tento certifikát použít pouze v počítači, jehož je **node2.domain.contoso.com**v y. ReQDN .
- Podporuje libovolnou velikost klíče podporovanou systémem Windows Server 2012 R2 pro certifikáty TLS/SSL.
- Certifikát používající klíče CNG není podporován.

#### <a name="faq-when-would-i-not-enable-this-encryption"></a>Časté dotazy: Kdy bych toto šifrování nepovolil?
Povolení šifrování může přidat určité náklady na vaši infrastrukturu (vlastnící veřejný certifikát), a proto můžete přeskočit povolení šifrování v níže uvedených případech:
- Pokud je integrační runtime spuštěn v důvěryhodné síti nebo v síti s průhledným šifrováním, jako je IP/SEC. Vzhledem k tomu, že tato komunikace kanálu je omezena pouze v rámci důvěryhodné sítě, pravděpodobně nebudete potřebovat další šifrování.
- Pokud není v produkčním prostředí spuštěn atožní doba integrace. To může pomoci při snižování nákladů na certifikát TLS/SSL.


## <a name="monitor-a-multi-node-gateway"></a>Sledování brány s více uzly
### <a name="multi-node-gateway-monitoring"></a>Monitorování brány s více uzly
Na webu Azure Portal můžete zobrazit snímek využití prostředků téměř v reálném čase (procesor, paměť, síť (dovnitř/ven) na každém uzlu spolu se stavy uzlů brány. 

![Brána pro správu dat – monitorování více uzlů](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)

Můžete povolit **rozšířené nastavení** na stránce **brána** zobrazíte rozšířené metriky, jako **je síť**(in / out), role & stav **pověření**, což je užitečné při ladění brány problémy a **souběžné úlohy** (Běh / Limit), které lze upravit nebo odpovídajícím způsobem změnit během ladění výkonu. V následující tabulce jsou uvedeny popisy sloupců v seznamu **Uzly brány:**  

Vlastnost sledování | Popis
:------------------ | :---------- 
Name (Název) | Název logické brány a uzlů přidružených k bráně.  
Status | Stav logické brány a uzlů brány. Příklad: Online/Offline/Limited/etc. Informace o těchto stavech naleznete v části [Stav brány.](#gateway-status) 
Version | Zobrazuje verzi logické brány a každého uzlu brány. Verze logické brány je určena na základě verze většiny uzlů ve skupině. Pokud jsou v nastavení logické brány uzly s různými verzemi, budou správně fungovat pouze uzly se stejným číslem verze jako logická brána. Ostatní jsou v omezeném režimu a je třeba je ručně aktualizovat (pouze v případě, že se automatická aktualizace nezdaří). 
Dostupná paměť | Dostupná paměť v uzlu brány. Tato hodnota je snímek téměř v reálném čase. 
Využití procesoru | Využití procesoru uzlu brány. Tato hodnota je snímek téměř v reálném čase. 
Síťové (příchozí/odchozí) | Využití uzlu brány v síti. Tato hodnota je snímek téměř v reálném čase. 
Souběžné úlohy (spuštěné/limitové) | Počet úloh nebo úloh spuštěných v každém uzlu. Tato hodnota je snímek téměř v reálném čase. Limit označuje maximální souběžné úlohy pro každý uzel. Tato hodnota je definována na základě velikosti stroje. Můžete zvýšit limit vertikálně navýšit souběžné provádění úloh v pokročilých scénářích, kde cpu / paměť / síť je nedostatečně využívána, ale aktivity jsou vypršení časového limitu. Tato funkce je také k dispozici s bránou s jedním uzlem (i když není povolena funkce škálovatelnosti a dostupnosti). Další informace naleznete v části [aspekty měřítka.](#scale-considerations) 
Role | Existují dva typy rolí – Dispečer a pracovník. Všechny uzly jsou pracovníci, což znamená, že všechny mohou být použity k provádění úloh. Existuje pouze jeden uzel dispečera, který se používá k vytahování úloh/úloh z cloudových služeb a jejich odeslání do různých uzlů pracovního procesu (včetně sebe sama). 

![Brána pro správu dat – pokročilé monitorování více uzlů](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-advanced.png)

### <a name="gateway-status"></a>Stav brány

Následující tabulka obsahuje možné stavy **uzlu brány**: 

Status  | Komentáře/scénáře
:------- | :------------------
Online | Uzel připojený ke službě Data Factory.
Offline | Uzel je offline.
Inovace | Uzel je automaticky aktualizován.
Omezeně | Kvůli problému s připojením. Může být způsobeno problémem s portem HTTP 8050, problémem s připojením sběrnice nebo problémem se synchronizací pověření. 
Inactive | Uzel je v konfiguraci odlišné od konfigurace jiných většinových uzlů.<br/><br/> Uzel může být neaktivní, pokud se nemůže připojit k jiným uzlům. 


Následující tabulka obsahuje možné stavy **logické brány**. Stav brány závisí na stavech uzlů brány. 

Status | Komentáře
:----- | :-------
Potřebuje registraci | Na tuto logickou bránu ještě není registrován žádný uzel.
Online | Uzly brány jsou online
Offline | Žádný uzel ve stavu online.
Omezeně | Ne všechny uzly v této bráně jsou v pořádku stavu. Tento stav je upozornění, že některé uzlmůže být dole! <br/><br/>Může být způsobeno problémem synchronizace pověření v uzlu dispečera/pracovníka. 

### <a name="pipeline-activities-monitoring"></a>Monitorování potrubí/činností
Portál Azure poskytuje prostředí pro monitorování kanálu s podrobnými podrobnostmi o úrovni uzlu. Například ukazuje, které aktivity běžel na který uzel. Tyto informace mohou být užitečné při pochopení problémů s výkonem v konkrétním uzlu, řekněme z důvodu omezení sítě. 

![Brána pro správu dat – monitorování více uzlů pro kanály](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-pipelines.png)

![Brána pro správu dat – podrobnosti o kanálu](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-pipeline-details.png)

## <a name="scale-considerations"></a>Důležité informace o měřítku

### <a name="scale-out"></a>Horizontální navýšení kapacity
Když **je nedostatek dostupné paměti** a **využití procesoru je vysoká**, přidání nového uzlu pomáhá horizontální navýšení kapacity zatížení napříč počítači. Pokud aktivity selhávají z důvodu časového výpadku nebo uzlu brány v offline, pomůže, pokud přidáte uzel do brány.
 
### <a name="scale-up"></a>Vertikální navýšení kapacity
Pokud dostupné paměti a procesoru nejsou využívány dobře, ale kapacita nečinnosti je 0, měli byste vertikálně navýšit kapacitu zvýšením počtu souběžných úloh, které lze spustit na uzlu. Můžete také vertikálně navýšit kapacitu, když jsou časování aktivit, protože brána je přetížena. Jak je znázorněno na následujícím obrázku, můžete zvýšit maximální kapacitu uzlu. Navrhujeme zdvojnásobit to začít.  

![Brána pro správu dat – aspekty škálování](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-scale-considerations.png)


## <a name="known-issuesbreaking-changes"></a>Známé problémy/změny

- V současné době můžete mít až čtyři uzly fyzické brány pro jednu logickou bránu. Pokud potřebujete více než čtyři uzly z [DMGHelp@microsoft.com](mailto:DMGHelp@microsoft.com)důvodů výkonu, pošlete e-mail na adresu .
- Uzel brány nelze znovu zaregistrovat pomocí ověřovacího klíče z jiné logické brány a přepnout z aktuální logické brány. Chcete-li znovu zaregistrovat, odinstalujte bránu z uzlu, přeinstalujte bránu a zaregistrujte ji pomocí ověřovacího klíče pro jinou logickou bránu. 
- Pokud je pro všechny uzly brány vyžadován proxy server HTTP, nastavte proxy server v souborech diahost.exe.config a diawp.exe.config a pomocí správce serveru zkontrolujte, zda všechny uzly mají stejné diahost.exe.config a diawip.exe.config. Podrobnosti naleznete v části [Konfigurace nastavení proxy serveru.](data-factory-data-management-gateway.md#configure-proxy-server-settings) 
- Chcete-li změnit režim šifrování pro komunikaci mezi uzly ve Správci konfigurace brány, odstraňte všechny uzly na portálu kromě jednoho. Potom přidejte uzly zpět po změně režimu šifrování.
- Pokud se rozhodnete šifrovat komunikační kanál mezi uzlem, použijte oficiální certifikát TLS. Certifikát podepsaný svým držitelem může způsobit problémy s připojením, protože stejný certifikát nemusí být důvěryhodný v seznamu certifikačních úřadů v jiných počítačích. 
- Uzel brány nelze zaregistrovat do logické brány, pokud je verze uzlu nižší než verze logické brány. Odstraňte všechny uzly logické brány z portálu, abyste mohli zaregistrovat nižší verzi uzlu (downgrade) to. Pokud odstraníte všechny uzly logické brány, ručně nainstalujte a zaregistrujte nové uzly do této logické brány. Expresní nastavení není v tomto případě podporováno.
- Expresní nastavení nelze použít k instalaci uzlů do existující logické brány, která stále používá cloudová pověření. Můžete zkontrolovat, kde jsou uloženy přihlašovací údaje z Správce konfigurace brány na kartě Nastavení.
- Expresní nastavení nelze použít k instalaci uzlů do existující logické brány, která má povoleno šifrování mezi uzly. Vzhledem k tomu, že nastavení režimu šifrování zahrnuje ruční přidávání certifikátů, expresní instalace již není možností. 
- Pro kopírování souborů z místního prostředí byste \\neměli používat localhost nebo C:\files anymore, protože localhost nebo místní jednotka nemusí být přístupná přes všechny uzly. Místo toho \\použijte serverName\files k určení umístění souborů.


## <a name="rolling-back-from-the-preview"></a>Vrácení zpět z náhledu 
Chcete-li se vrátit zpět z náhledu, odstraňte všechny uzly kromě jednoho uzlu. Nezáleží na tom, které uzly odstraníte, ale ujistěte se, že máte alespoň jeden uzel v logické bráně. Uzel můžete odstranit buď odinstalováním brány v počítači nebo pomocí portálu Azure. Na webu Azure Portal klikněte na stránce **Data Factory** na Propojené služby a spusťte stránku **Propojené služby.** Vyberte bránu, chcete-li spustit stránku **Brána.** Na stránce Brána uvidíte uzly přidružené k bráně. Stránka umožňuje odstranit uzel z brány.
 
Po odstranění klikněte na **funkce náhledu** na stejné stránce portálu Azure a zakažte funkci náhledu. Obnovili jste bránu na jednu bránu GA uzlu (obecná dostupnost).


## <a name="next-steps"></a>Další kroky
Prohlédněte si následující články:
- [Brána pro správu dat](data-factory-data-management-gateway.md) - poskytuje podrobný přehled o bráně.
- [Přesun dat mezi místními a cloudovými úložišti dat](data-factory-move-data-between-onprem-and-cloud.md) – obsahuje návod s podrobnými pokyny pro použití brány s jedním uzly. 
