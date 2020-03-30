---
title: Návrh nasazení protokolů azure monitoru | Dokumenty společnosti Microsoft
description: Tento článek popisuje aspekty a doporučení pro zákazníky, kteří se připravují na nasazení pracovního prostoru ve službě Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/20/2019
ms.openlocfilehash: e493b07814821496f941a4b81402ba0b49acbede
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248824"
---
# <a name="designing-your-azure-monitor-logs-deployment"></a>Návrh nasazení protokolů azure monitoru

Azure Monitor ukládá data [protokolu](data-platform-logs.md) v pracovním prostoru Log Analytics, což je prostředek Azure a kontejner, kde se data shromažďují, agregují a slouží jako hranice pro správu. I když můžete nasadit jeden nebo více pracovních prostorů ve vašem předplatném Azure, existuje několik aspektů, které byste měli pochopit, abyste zajistili, že vaše počáteční nasazení bude dodržovat naše pokyny, aby vám poskytlo nákladově efektivní, spravovatelné a škálovatelné nasazení, které splňuje potřeby vašich organizací.

Data v pracovním prostoru jsou uspořádána do tabulek, z nichž každá ukládá různé druhy dat a má vlastní jedinečnou sadu vlastností na základě prostředků generujících data. Většina zdrojů dat bude zapisovat do vlastních tabulek v pracovním prostoru Log Analytics.

![Příklad datového modelu pracovního prostoru](./media/design-logs-deployment/logs-data-model-01.png)

Pracovní prostor Analýzy protokolů poskytuje:

* Geografické umístění pro ukládání dat.
* Izolace dat tím, že různým uživatelům udělíme přístupová práva podle jedné z našich doporučených strategií návrhu.
* Obor pro konfiguraci nastavení, jako [je cenová úroveň](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier), [uchovávání](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)a [omezení dat](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#manage-your-maximum-daily-data-volume).

Tento článek obsahuje podrobný přehled aspekty návrhu a migrace, přehled řízení přístupu a pochopení implementací návrhu, které doporučujeme pro vaši organizaci IT.



## <a name="important-considerations-for-an-access-control-strategy"></a>Důležité aspekty strategie řízení přístupu

Identifikace počtu pracovních prostorů, které potřebujete, je ovlivněna jedním nebo více z následujících požadavků:

* Jste globální společnost a potřebujete data protokolu uložená v konkrétních oblastech z důvodů suverenity dat nebo dodržování předpisů.
* Používáte Azure a chcete se vyhnout poplatkům za odchozí datové přenosy tím, že budete mít pracovní prostor ve stejné oblasti jako prostředky Azure, které spravuje.
* Spravujete více oddělení nebo obchodních skupin a chcete, aby každý z nich viděl svá vlastní data, ale ne data od ostatních. Neexistuje také žádný obchodní požadavek pro konsolidované zobrazení mezi odděleními nebo obchodní skupinou.

IT organizace jsou dnes modelovány podle centralizovaného, decentralizovaného nebo mezihybridu obou struktur. V důsledku toho se k mapování na jednu z těchto organizačních struktur běžně používají následující modely nasazení pracovního prostoru:

* **Centralizované**: Všechny protokoly jsou uloženy v centrálním pracovním prostoru a spravovány jedním týmem, s Azure Monitor poskytuje diferencovaný přístup na tým. V tomto scénáři je snadné spravovat, hledání napříč prostředky a křížovou korelovat protokoly. Pracovní prostor může výrazně růst v závislosti na množství dat shromážděných z více prostředků ve vašem předplatném, s další administrativní režie pro udržení řízení přístupu pro různé uživatele. Tento model se nazývá "rozbočovač a paprsek".
* **Decentralizovaný**: Každý tým má svůj vlastní pracovní prostor vytvořený ve skupině prostředků, kterou vlastní a spravuje, a data protokolu jsou oddělena podle prostředku. V tomto scénáři lze zajistit zabezpečení pracovního prostoru a řízení přístupu je konzistentní s přístupem k prostředkům, ale je obtížné křížově korelovat protokoly. Uživatelé, kteří potřebují široký přehled o mnoha prostředcích, nemohou data smysluplně analyzovat.
* **Hybridní**: Požadavky na dodržování předpisů auditu zabezpečení dále komplikují tento scénář, protože mnoho organizací implementuje oba modely nasazení paralelně. To obvykle vede ke složité, nákladné a těžko udržovatelné konfiguraci s mezerami v pokrytí protokolů.

Při použití agentů Log Analytics ke shromažďování dat, musíte pochopit následující, abyste mohli naplánovat nasazení agenta:

* Chcete-li shromažďovat data od agentů systému Windows, můžete [nakonfigurovat každého agenta tak, aby se hlásil do jednoho nebo více pracovních prostorů](../../azure-monitor/platform/agent-windows.md), a to i v případě, že je vykazován skupině pro správu nástroje System Center Operations Manager. Agent systému Windows může hlásit až čtyři pracovní prostory.
* Agent Linuxu nepodporuje vícenásobné navádění a může se hlásit pouze do jednoho pracovního prostoru.

Pokud používáte nástroj System Center Operations Manager 2012 R2 nebo novější:

* Každá skupina správy nástroje Operations Manager lze [připojit pouze k jednomu pracovnímu prostoru](../platform/om-agents.md). 
* Počítače s Linuxem, které se hlásí skupině pro správu, musí být nakonfigurovány tak, aby se hlásily přímo do pracovního prostoru Analýzy protokolů. Pokud vaše počítače s Linuxem již vykazují hlášení přímo do pracovního prostoru a chcete je sledovat pomocí nástroje Operations Manager, [nahlaste](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group)je podle pokynů skupině pro správu nástroje Operations Manager . 
* Agenta systému Windows pro analýzu protokolů můžete nainstalovat do počítače se systémem Windows a nechat jej nahlásit jak nástroji Operations Manager, integrovaným s pracovním prostorem, tak do jiného pracovního prostoru.

## <a name="access-control-overview"></a>Přehled řízení přístupu

Pomocí řízení přístupu na základě rolí (RBAC) můžete uživatelům a skupinám udělit pouze množství přístupu, které potřebují k práci s daty monitorování v pracovním prostoru. To vám umožní zarovnat s provozním modelem vaší organizace IT pomocí jednoho pracovního prostoru pro ukládání shromážděných dat povolených ve všech vašich prostředcích. Například udělíte přístup svému týmu odpovědnému za služby infrastruktury hostované na virtuálních počítačích Azure (VM) a v důsledku toho budou mít přístup k jenom protokolům generovaným virtuálními počítači. Toto je následující náš nový model protokolu kontextu prostředků. Základem pro tento model je pro každý záznam protokolu vyzařovaného prostředek Azure, je automaticky přidružen k tomuto prostředku. Protokoly jsou předávány do centrálního pracovního prostoru, který respektuje obora a RBAC na základě prostředků.

Data, ke kterým má uživatel přístup, jsou určena kombinací faktorů, které jsou uvedeny v následující tabulce. Každý z nich je popsán v následujících částech.

| Faktor | Popis |
|:---|:---|
| [Režim přístupu](#access-mode) | Metoda, kterou uživatel používá pro přístup k pracovnímu prostoru.  Definuje rozsah dostupných dat a režim řízení přístupu, který je použit. |
| [Režim řízení přístupu](#access-control-mode) | Nastavení pracovního prostoru, který definuje, zda jsou oprávnění použita na úrovni pracovního prostoru nebo prostředků. |
| [Oprávnění](manage-access.md) | Oprávnění použitá pro jednotlivé nebo skupiny uživatelů pracovního prostoru nebo prostředku. Definuje, k jakým datům bude mít uživatel přístup. |
| [Úroveň tabulky RBAC](manage-access.md#table-level-rbac) | Volitelná podrobná oprávnění, která platí pro všechny uživatele bez ohledu na jejich režim přístupu nebo režim řízení přístupu. Definuje, ke kterým datovým typům má uživatel přístup. |

## <a name="access-mode"></a>Režim přístupu

*Režim přístupu* odkazuje na způsob, jakým uživatel přistupuje k pracovnímu prostoru Log Analytics a definuje rozsah dat, ke kterým má přístup. 

Uživatelé mají dvě možnosti přístupu k datům:

* **Kontext pracovního prostoru**: Můžete zobrazit všechny protokoly v pracovním prostoru, ke které máte oprávnění. Dotazy v tomto režimu jsou vymezeny na všechna data ve všech tabulkách v pracovním prostoru. Toto je režim přístupu používaný při přístupu k protokolům s pracovním prostorem jako oborem, například když vyberete **protokoly** z nabídky **Azure Monitor** na webu Azure Portal.

    ![Kontext analýzy protokolů z pracovního prostoru](./media/design-logs-deployment/query-from-workspace.png)

* **Kontext prostředků**: Při přístupu k pracovnímu prostoru pro konkrétní prostředek, skupinu prostředků nebo předplatné, například když vyberete **protokoly** z nabídky prostředků na webu Azure Portal, můžete zobrazit protokoly pouze prostředky ve všech tabulkách, které máte přístup. Dotazy v tomto režimu jsou vymezeny pouze na data přidružená k tomuto prostředku. Tento režim také umožňuje granulární RBAC.

    ![Kontext analýzy protokolů z prostředku](./media/design-logs-deployment/query-from-resource.png)

    > [!NOTE]
    > Protokoly jsou k dispozici pro dotazy kontextu prostředků pouze v případě, že byly správně přidruženy k příslušnému prostředku. V současné době mají následující zdroje omezení:
    > - Počítače mimo Azure
    > - Service Fabric
    > - Application Insights
    >
    > Můžete otestovat, zda protokoly jsou správně přidruženy k jejich prostředku spuštěním dotazu a kontrolou záznamů, které vás zajímají. Pokud je správné ID prostředku ve [vlastnosti _ResourceId,](log-standard-properties.md#_resourceid) jsou data k dispozici pro dotazy zaměřené na prostředky.

Azure Monitor automaticky určuje správný režim v závislosti na kontextu, ze kterých provádíte hledání protokolu. Obor je vždy uveden v levé horní části Log Analytics.

### <a name="comparing-access-modes"></a>Porovnání režimů přístupu

Následující tabulka shrnuje režimy přístupu:

| | Kontext pracovního prostoru | Kontext zdrojů |
|:---|:---|:---|
| Pro koho je každý model určen? | Ústřední správa. Správci, kteří potřebují konfigurovat shromažďování dat a uživatelé, kteří potřebují přístup k široké škále prostředků. Aktuálně se to vynuťuje i pro uživatele, kteří potřebují přístup k protokolům pro prostředky mimo Azure. | Aplikační týmy. Správci sledovaných prostředků Azure. |
| Co uživatel vyžaduje k zobrazení protokolů? | Oprávnění k pracovnímu prostoru. Viz **Oprávnění pracovního prostoru** v [tématu Správa přístupu pomocí oprávnění pracovního prostoru](manage-access.md#manage-access-using-workspace-permissions). | Přístup pro čtení k prostředku. Viz **Oprávnění k prostředkům** v [tématu Správa přístupu pomocí oprávnění Azure](manage-access.md#manage-access-using-azure-permissions). Oprávnění mohou být zděděna (například ze skupiny obsahujících prostředků) nebo přímo přiřazena k prostředku. Oprávnění k protokolům pro prostředek bude automaticky přiřazeno. |
| Jaký je rozsah oprávnění? | Pracovního prostoru. Uživatelé s přístupem k pracovnímu prostoru mohou dotazovat všechny protokoly v pracovním prostoru z tabulek, ke kterým mají oprávnění. Viz [Ovládací prvek přístupu k tabulce](manage-access.md#table-level-rbac) | Prostředek Azure. Uživatel může dotazovat protokoly pro konkrétní prostředky, skupiny prostředků nebo předplatné, ke kterým má přístup z libovolného pracovního prostoru, ale nemůže dotazovat protokoly pro jiné prostředky. |
| Jak může uživatel získat přístup k protokolům? | <ul><li>Spuštění **protokolů** z nabídky **Azure Monitor.**</li></ul> <ul><li>Spusťte **protokoly** z **pracovních prostorů Analýzy protokolů**.</li></ul> <ul><li>Ze [sešitů](../visualizations.md#workbooks)Azure Monitor .</li></ul> | <ul><li>Spustit **protokoly** z nabídky pro prostředek Azure</li></ul> <ul><li>Spuštění **protokolů** z nabídky **Azure Monitor.**</li></ul> <ul><li>Spusťte **protokoly** z **pracovních prostorů Analýzy protokolů**.</li></ul> <ul><li>Ze [sešitů](../visualizations.md#workbooks)Azure Monitor .</li></ul> |

## <a name="access-control-mode"></a>Režim řízení přístupu

*Režim řízení přístupu* je nastavení v každém pracovním prostoru, které definuje způsob určení oprávnění pro pracovní prostor.

* **Vyžadovat oprávnění pracovního prostoru**: Tento režim ovládacího prvku neumožňuje granulovaný RBAC. Aby měl uživatel přístup k pracovnímu prostoru, musí mu být udělena oprávnění k pracovnímu prostoru nebo konkrétním tabulkám.

    Pokud uživatel přistupuje k pracovnímu prostoru podle režimu kontextu pracovního prostoru, mají přístup ke všem datům v libovolné tabulce, ke které mu byl udělen přístup. Pokud uživatel přistupuje k pracovnímu prostoru podle režimu kontextu prostředku, mají přístup pouze k datům pro tento prostředek v libovolné tabulce, ke které byl udělen přístup.

    Toto je výchozí nastavení pro všechny pracovní prostory vytvořené před březnem 2019.

* **Použít oprávnění k prostředkům nebo pracovnímu prostoru**: Tento režim řízení umožňuje granulovaný RBAC. Uživatelům lze udělit přístup k pouze datům přidruženým `read` k prostředkům, které mohou zobrazit přiřazením oprávnění Azure. 

    Když uživatel přistupuje k pracovnímu prostoru v režimu kontextu pracovního prostoru, použijí se oprávnění pracovního prostoru. Když uživatel přistupuje k pracovnímu prostoru v režimu kontextu prostředků, ověří se pouze oprávnění k prostředkům a oprávnění pracovního prostoru se ignorují. Povolte RBAC pro uživatele odebráním z oprávnění pracovního prostoru a povolením jejich oprávnění k prostředkům, které mají být rozpoznány.

    Toto je výchozí nastavení pro všechny pracovní prostory vytvořené po březnu 2019.

    > [!NOTE]
    > Pokud má uživatel pouze oprávnění k prostředkům k pracovnímu prostoru, má k pracovnímu prostoru přístup pouze pomocí režimu kontextu prostředků za předpokladu, že je režim přístupu k pracovnímu prostoru nastaven na **použití oprávnění k prostředkům nebo pracovnímu prostoru**.

Informace o změně režimu řízení přístupu na portálu pomocí prostředí PowerShell nebo pomocí šablony Správce prostředků naleznete v [tématu Konfigurace režimu řízení přístupu](manage-access.md#configure-access-control-mode).

## <a name="ingestion-volume-rate-limit"></a>Limit rychlosti rychlosti požití

Azure Monitor je datová služba ve velkém měřítku, která poskytuje tisíce zákazníků odesílajících každý měsíc terabajty dat rostoucím tempem. Výchozí prahová hodnota rychlosti přizdání je nastavena na **6 GB/min** na pracovní plochu. Jedná se o přibližnou hodnotu, protože skutečná velikost se může lišit mezi datovými typy v závislosti na délce protokolu a jeho kompresním poměru. Toto omezení se nevztahuje na data odesílaná z agentů nebo [rozhraní API pro kolekcí dat](data-collector-api.md).

Pokud odesíláte data vyšší rychlostí do jednoho pracovního prostoru, některá data jsou vynechána a událost je odeslána do tabulky *Operace* v pracovním prostoru každých 6 hodin, zatímco prahová hodnota je stále překročena. Pokud svazek ingestování stále překračuje limit rychlosti nebo očekáváte, že k němu někdy brzy dosáhnete, můžete požádat o zvýšení pracovního prostoru otevřením žádosti o podporu.
 
Chcete-li být upozorněni na takovou událost v pracovním prostoru, vytvořte [pravidlo výstrahprotokolu](alerts-log.md) pomocí následujícího dotazu s logikou výstrahy, která je základem pro počet strmač výsledků než nula.

``` Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The rate of data crossed the threshold"
``` 


## <a name="recommendations"></a>Doporučení

![Příklad návrhu kontextu prostředků](./media/design-logs-deployment/workspace-design-resource-context-01.png)

Tento scénář zahrnuje jeden návrh pracovního prostoru v předplatném IT organizací, který není omezen suverenitou dat nebo dodržováním předpisů nebo potřebuje mapovat do oblastí, ve kterých jsou vaše prostředky nasazeny. Umožňuje vašim organizacím zabezpečení a týmům pro správu IT možnost využít vylepšenou integraci se správou přístupu Azure a bezpečnějším řízením přístupu.

Všechny prostředky, řešení monitorování a přehledy, jako jsou Application Insights a Azure Monitor pro virtuální počítače, podpora infrastruktury a aplikací spravovaných různými týmy jsou nakonfigurované tak, aby přesměrovaly shromážděná data protokolu it organizacím centralizovaný sdílený pracovní prostor. Uživatelům v každém týmu je udělen přístup k protokolům pro prostředky, ke kterým jim byl udělen přístup.

Po nasazení architektury pracovního prostoru to můžete vynucovat u prostředků Azure pomocí [Azure Policy](../../governance/policy/overview.md). Poskytuje způsob, jak definovat zásady a zajistit dodržování vašich prostředků Azure tak, aby odesílají všechny své protokoly prostředků do určitého pracovního prostoru. Například s virtuálními počítači Azure nebo škálovacísady virtuálních počítačů můžete použít existující zásady, které vyhodnocují dodržování předpisů pracovního prostoru a výsledky sestav nebo přizpůsobit tak, aby se napravily, pokud nejsou kompatibilní.  

## <a name="workspace-consolidation-migration-strategy"></a>Strategie migrace konsolidace pracovního prostoru

Pro zákazníky, kteří již nasadili více pracovních prostorů a mají zájem o konsolidaci k modelu přístupu kontextu prostředků, doporučujeme provést přírůstkový přístup k migraci na doporučený model přístupu a nepokoušejte se toho dosáhnout rychle nebo agresivně. Po postupném přístupu k plánování, migraci, ověření a vyřazení na základě přiměřené časové osy pomůže vyhnout se neplánovaným incidentům nebo neočekávanému dopadu na vaše operace cloudu. Pokud nemáte zásady uchovávání dat z důvodu dodržování předpisů nebo obchodních důvodů, je třeba posoudit vhodnou dobu uchování dat v pracovním prostoru, ze které během procesu migrujete. Při rekonfiguraci prostředků pro vykazování do sdíleného pracovního prostoru můžete podle potřeby analyzovat data v původním pracovním prostoru. Po dokončení migrace, pokud se řídí uchovávat data v původním pracovním prostoru před koncem období uchovávání, neodstraňujte ji.

Při plánování migrace na tento model zvažte následující:

* Pochopte, jaké oborové předpisy a vnitřní zásady týkající se uchovávání dat musíte dodržovat.
* Ujistěte se, že vaše týmy aplikací mohou pracovat v rámci existující funkce kontextu prostředků.
* Identifikujte přístup udělený prostředkům pro vaše aplikační týmy a otestujte je ve vývojovém prostředí před implementací v produkčním prostředí.
* Nakonfigurujte pracovní prostor tak, aby **povoloval použití oprávnění k prostředkům nebo pracovnímu prostoru**.
* Odeberte oprávnění aplikačních týmů ke čtení a dotazování pracovního prostoru.
* Povolte a nakonfigurujte všechna řešení monitorování, přehledy, jako je Azure Monitor pro kontejnery a/nebo Azure Monitor pro virtuální počítače, vaše účty automatizace a řešení pro správu, jako je správa aktualizací, spuštění a zastavení virtuálních počítačů atd., které byly nasazeny v původním pracovním prostoru.

## <a name="next-steps"></a>Další kroky

Chcete-li implementovat oprávnění zabezpečení a ovládací prvky doporučené v této příručce, [zkontrolujte, že správa přístupu k protokolům](manage-access.md).
