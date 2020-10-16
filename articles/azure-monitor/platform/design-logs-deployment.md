---
title: Navrhování nasazení Azure Monitorch protokolů | Microsoft Docs
description: Tento článek popisuje pokyny a doporučení pro zákazníky, kteří připravují nasazení pracovního prostoru v Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/20/2019
ms.openlocfilehash: 21da883867da41e81ed1787faa0ebe0e6dd25d99
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92107874"
---
# <a name="designing-your-azure-monitor-logs-deployment"></a>Návrh nasazení protokolů služby Azure Monitor

Azure Monitor ukládá data [protokolu](data-platform-logs.md) v pracovním prostoru Log Analytics, což je prostředek Azure a kontejner, ve kterém se data shromažďují, agreguje a slouží jako hranice správy. I když můžete v předplatném Azure nasadit jeden nebo více pracovních prostorů, měli byste pochopit několik důležitých informací, abyste měli jistotu, že vaše počáteční nasazení probíhá podle našich pokynů, které vám poskytnou nákladově efektivní, spravovatelné a škálovatelné nasazení splňující požadavky vaší organizace.

Data v pracovním prostoru jsou uspořádaná do tabulek, z nichž každý ukládá různé druhy dat a má svou vlastní jedinečnou sadu vlastností založenou na prostředku, který data generuje. Většina zdrojů dat bude zapisovat do vlastních tabulek v pracovním prostoru Log Analytics.

![Ukázkový datový model pracovního prostoru](./media/design-logs-deployment/logs-data-model-01.png)

Pracovní prostor Log Analytics poskytuje:

* Geografické umístění pro ukládání dat.
* Izolace dat udělením přístupových práv k různým uživatelům podle jedné z našich doporučených strategií návrhu.
* Obor pro konfiguraci nastavení, jako je [cenová úroveň](./manage-cost-storage.md#changing-pricing-tier), [uchování](./manage-cost-storage.md#change-the-data-retention-period)a [capping dat](./manage-cost-storage.md#manage-your-maximum-daily-data-volume)

Pracovní prostory jsou hostovány na fyzických clusterech. Ve výchozím nastavení systém vytváří a spravuje tyto clustery. Pro zákazníky, kteří ingestují víc než 4 TB/den, se očekává, že si pro své pracovní prostory vytvoří vlastní vyhrazená clustery – umožní jim lepší řízení a vyšší rychlost přijímání.

Tento článek poskytuje podrobný přehled týkající se návrhu a migrace, přehled řízení přístupu a porozumění implementací návrhu, které doporučujeme pro vaši organizaci IT.



## <a name="important-considerations-for-an-access-control-strategy"></a>Důležité informace o strategii řízení přístupu

Určení počtu pracovních prostorů, které potřebujete, je ovlivněno jedním nebo více následujícími požadavky:

* Jste globální společnost a potřebujete protokolovat data uložená v určitých oblastech pro účely svrchovanosti nebo dodržování předpisů.
* Používáte Azure a chcete se vyhnout poplatkům za odchozí datové přenosy tím, že budete mít pracovní prostor ve stejné oblasti jako prostředky Azure, které spravuje.
* Spravujete více oddělení nebo obchodních skupin a chcete, aby každý z nich viděl svá vlastní data, ale ne data jiných. Pro konsolidované zobrazení mezi odděleními nebo obchodními skupinami neexistuje žádný požadavek na obchod.

IT organizace jsou v současnosti modelované v rámci centralizované, decentralizované nebo mezi hybridem obou struktur. V důsledku toho se běžně používají následující modely nasazení pracovního prostoru k mapování jedné z těchto organizačních struktur:

* **Centralizované**: všechny protokoly jsou uloženy v centrálním pracovním prostoru a spravovány jedním týmem, a Azure monitor poskytují rozlišený přístup pro každý tým. V tomto scénáři se dá snadno spravovat, Hledat v různých zdrojích a protokoly napříč korelacemi. Pracovní prostor se může významně zvýšit v závislosti na množství dat shromážděných z několika prostředků v rámci vašeho předplatného, s dodatečnými administrativními režijními náklady na řízení přístupu pro různé uživatele. Tento model se označuje jako hub a paprsek.
* **Decentralizované**: každý tým má vlastní pracovní prostor vytvořený ve skupině prostředků, kterou vlastní a spravuje, a data protokolu se oddělí na jeden prostředek. V tomto scénáři je možné pracovní prostor uchovat zabezpečeně a řízení přístupu je konzistentní s přístupem k prostředkům, ale je obtížné protokolovat mezi korelacemi. Uživatelé, kteří potřebují široké zobrazení mnoha prostředků, nemůžou analyzovat data smysluplným způsobem.
* **Hybridní**: požadavky na dodržování předpisů audit zabezpečení dále komplikuje tento scénář, protože mnoho organizací implementuje současně oba modely nasazení. To obvykle vede ke složitým, nákladným a pevným konfiguracím, a to s mezerami v protokolech pokrytí.

Při použití agentů Log Analytics ke shromažďování dat je potřeba pochopit následující informace, aby bylo možné naplánovat nasazení agenta:

* Chcete-li shromažďovat data z agentů systému Windows, můžete [nakonfigurovat každého agenta tak, aby se nahlásily do jednoho nebo více pracovních prostorů](./agent-windows.md), a to i v případě, že se hlásí do skupiny pro správu System Center Operations Manager Agent pro Windows může hlásit až čtyři pracovní prostory.
* Agent pro Linux nepodporuje více domovských stránek a může nahlásit pouze jeden pracovní prostor.

Pokud používáte System Center Operations Manager 2012 R2 nebo novější:

* Každá skupina pro správu Operations Manager může být [připojená pouze k jednomu pracovnímu prostoru](./om-agents.md). 
* Počítače se systémem Linux vykazující skupinu pro správu musí být nakonfigurovány tak, aby nahlásily přímo do Log Analytics pracovního prostoru. Pokud již počítače se systémem Linux hlásí přímo do pracovního prostoru a chcete je monitorovat pomocí Operations Manager, postupujte podle těchto kroků a [nahlaste se skupině pro správu Operations Manager](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group). 
* Log Analytics agenta pro Windows můžete nainstalovat do počítače s Windows a podávat ho do obou Operations Manager integrovaných v pracovním prostoru a v jiném pracovním prostoru.

## <a name="access-control-overview"></a>Přehled řízení přístupu

Díky řízení přístupu na základě role (RBAC) můžete uživatelům a skupinám udělit jenom množství přístupu, které potřebují k práci s daty monitorování v pracovním prostoru. Díky tomu můžete s modelem vaší organizace IT v souladu s operačním modelem IT v jednom pracovním prostoru ukládat shromážděná data, která jsou povolená pro všechny vaše prostředky. Například udělíte vašemu týmu přístup, který je zodpovědný za služby infrastruktury hostované na virtuálních počítačích Azure. v důsledku toho budou mít přístup jenom k protokolům generovaným virtuálními počítači. Toto je podle našeho nového modelu protokolu kontextu prostředků. Základem pro tento model je každý záznam protokolu generovaný prostředkem Azure, který se automaticky přidruží k tomuto prostředku. Protokoly se předávají do centrálního pracovního prostoru, který respektuje rozsah a RBAC na základě prostředků.

Data, ke kterým má uživatel přístup, závisí na kombinaci faktorů, které jsou uvedeny v následující tabulce. Jednotlivé jsou popsány v následujících částech.

| Faktor | Popis |
|:---|:---|
| [Režim přístupu](#access-mode) | Metoda, kterou uživatel používá pro přístup k pracovnímu prostoru.  Definuje rozsah dostupných dat a režim řízení přístupu, který se použije. |
| [Režim řízení přístupu](#access-control-mode) | Nastavení v pracovním prostoru definující, zda jsou oprávnění použita na úrovni pracovního prostoru nebo prostředku. |
| [Oprávnění](manage-access.md) | Oprávnění použitá pro jednotlivé nebo skupiny uživatelů pro pracovní prostor nebo prostředek. Definuje data, ke kterým má uživatel přístup. |
| [RBAC na úrovni tabulky](manage-access.md#table-level-rbac) | Volitelná podrobné oprávnění, která platí pro všechny uživatele bez ohledu na jejich režim přístupu nebo režim řízení přístupu. Definuje typy dat, ke kterým má uživatel přístup. |

## <a name="access-mode"></a>Režim přístupu

*Režim přístupu* odkazuje na to, jak uživatel přistupuje k pracovnímu prostoru Log Analytics a definuje rozsah dat, ke kterým mají přístup. 

Uživatelé mají dvě možnosti pro přístup k datům:

* **Pracovní prostor – kontext**: můžete zobrazit všechny protokoly v pracovním prostoru, ke kterému máte oprávnění. Dotazy v tomto režimu jsou vymezeny na všechna data ve všech tabulkách v pracovním prostoru. Jedná se o režim přístupu, který se používá v případě, že se k protokolům přistupuje v pracovním prostoru jako s oborem, například při výběru **protokolů** z nabídky **Azure monitor** v Azure Portal.

    ![Log Analytics kontext z pracovního prostoru](./media/design-logs-deployment/query-from-workspace.png)

* **Prostředek-kontext**: Když přistupujete k pracovnímu prostoru pro konkrétní prostředek, skupinu prostředků nebo předplatné, například když v Azure Portal vyberete **protokoly** z nabídky prostředku, můžete zobrazit protokoly jenom pro prostředky ve všech tabulkách, ke kterým máte přístup. Dotazy v tomto režimu jsou vymezeny jenom na data přidružená k tomuto prostředku. Tento režim také umožňuje detailní RBAC.

    ![Log Analytics kontext z prostředku](./media/design-logs-deployment/query-from-resource.png)

    > [!NOTE]
    > Protokoly jsou k dispozici pro dotazy kontextu prostředků pouze v případě, že byly správně přidruženy k příslušnému prostředku. V současné době mají tato omezení následující zdroje:
    > - Počítače mimo Azure
    > - Service Fabric
    > - Application Insights
    >
    > Spuštěním dotazu a kontrolou záznamů, které vás zajímají, můžete otestovat, jestli jsou protokoly správně přidružené ke svým prostředkům. Pokud je ve vlastnosti [_ResourceId](./log-standard-columns.md#_resourceid) správné ID prostředku, data jsou k dispozici pro dotazy orientované na prostředky.

Azure Monitor automaticky určuje správný režim v závislosti na kontextu, ze kterého provádíte prohledávání protokolu. Obor je vždy zobrazen v levém horním oddílu Log Analytics.

### <a name="comparing-access-modes"></a>Porovnání režimů přístupu

Režimy přístupu jsou shrnuté v následující tabulce:

| Problém | Pracovní prostor – kontext | Prostředek – kontext |
|:---|:---|:---|
| Pro koho je každý model určen? | Centrální správa. Správci, kteří potřebují nakonfigurovat shromažďování dat a uživatele, kteří potřebují přístup k nejrůznějším prostředkům. V současnosti se vyžaduje pro uživatele, kteří potřebují přístup k protokolům pro prostředky mimo Azure. | Týmy aplikace Správci prostředků Azure, které jsou monitorovány. |
| Co uživatel potřebuje k zobrazení protokolů? | Oprávnění k pracovnímu prostoru. Oprávnění k pracovnímu prostoru najdete v tématu **oprávnění** [ke správě přístupu pomocí oprávnění pracovního prostoru](manage-access.md#manage-access-using-workspace-permissions). | Přístup pro čtení k prostředku. Podívejte se na téma **oprávnění prostředků** v tématu [Správa přístupu pomocí oprávnění Azure](manage-access.md#manage-access-using-azure-permissions). Oprávnění lze zdědit (například z obsahující skupiny prostředků) nebo přímo přiřadit prostředku. Automaticky se přiřadí oprávnění k protokolům pro daný prostředek. |
| Jaký je rozsah oprávnění? | Stejných. Uživatelé s přístupem k pracovnímu prostoru mohou dotazovat všechny protokoly v pracovním prostoru z tabulek, ke kterým mají oprávnění. Viz [řízení přístupu k tabulce](manage-access.md#table-level-rbac) | Prostředek Azure. Uživatel může zadat dotaz na protokoly pro konkrétní prostředky, skupiny prostředků nebo předplatné, ke kterým mají přístup z libovolného pracovního prostoru, ale nedokáže dotazovat protokoly na jiné prostředky. |
| Jak můžou protokoly přístupu uživatele? | <ul><li>Spusťte **protokoly** z nabídky **Azure monitor** .</li></ul> <ul><li>Spustí **protokoly** z **Log Analytics pracovní prostory**.</li></ul> <ul><li>Z Azure Monitor [sešitů](../visualizations.md#workbooks).</li></ul> | <ul><li>Spuštění **protokolů** z nabídky pro prostředek Azure</li></ul> <ul><li>Spusťte **protokoly** z nabídky **Azure monitor** .</li></ul> <ul><li>Spustí **protokoly** z **Log Analytics pracovní prostory**.</li></ul> <ul><li>Z Azure Monitor [sešitů](../visualizations.md#workbooks).</li></ul> |

## <a name="access-control-mode"></a>Režim řízení přístupu

*Režim řízení přístupu* je nastavení v každém pracovním prostoru definující způsob určování oprávnění pro pracovní prostor.

* **Vyžadovat oprávnění k pracovnímu prostoru**: Tento režim řízení neumožňuje podrobnou RBAC. Pro uživatele, kteří mají přístup k pracovnímu prostoru, musí mít udělená oprávnění k pracovnímu prostoru nebo určitým tabulkám.

    Pokud uživatel přistupuje k pracovnímu prostoru, který následuje za pracovním prostorem, má přístup ke všem datům v libovolné tabulce, ke které byl udělen přístup. Pokud uživatel přistupuje k pracovnímu prostoru, který následuje po kontextu prostředku, má přístup pouze k datům tohoto prostředku v libovolné tabulce, ke které byl udělen přístup.

    Toto je výchozí nastavení pro všechny pracovní prostory vytvořené před březen 2019.

* **Použít oprávnění prostředku nebo pracovního prostoru**: Tento režim řízení umožňuje detailní RBAC. Uživatelům se dá udělit přístup jenom k datům přidruženým k prostředkům, které můžou zobrazit, a to přiřazením `read` oprávnění Azure. 

    Když uživatel přistupuje k pracovnímu prostoru v pracovním prostoru – kontextový režim, platí oprávnění pracovního prostoru. Když uživatel přistupuje k pracovnímu prostoru v režimu v kontextu prostředků, ověřují se jenom oprávnění prostředků a oprávnění pracovního prostoru se ignorují. Povolte RBAC pro uživatele odebráním z oprávnění pracovního prostoru a povolením rozpoznání jejich oprávnění k prostředkům.

    Toto je výchozí nastavení pro všechny pracovní prostory vytvořené po březnu 2019.

    > [!NOTE]
    > Pokud má uživatel pouze oprávnění k pracovnímu prostoru, má přístup k pracovnímu prostoru pouze pomocí režimu kontextu prostředků, předpokládá se, že režim přístupu k pracovnímu prostoru je nastaven na **používání prostředků nebo oprávnění v pracovním prostoru**.

Informace o tom, jak změnit režim řízení přístupu na portálu, pomocí PowerShellu nebo pomocí šablony Správce prostředků, najdete v tématu [Konfigurace režimu řízení přístupu](manage-access.md#configure-access-control-mode).

## <a name="scale-and-ingestion-volume-rate-limit"></a>Omezení rychlosti škálování a ingestování

Azure Monitor je služba data ve velkém měřítku, která slouží tisícům zákazníků, kteří posílají petabajty data každý měsíc rostoucím tempem. Pracovní prostory nejsou v prostoru úložiště omezené a můžou se rozšířit na petabajty dat. Z důvodu škálování není nutné rozdělit pracovní prostory.

Pokud chcete chránit a izolovat Azure Monitor zákazníky a jeho infrastrukturu back-endu, je k dispozici výchozí limit pro přijímání zpráv, který je určený k ochraně před špičkami a zahlcením. Výchozí hodnota omezení četnosti je **6 GB/min** a je navržena tak, aby umožňovala normální přijímání. Další podrobnosti o měřeních velikosti svazků pro přijímání najdete v tématu [omezení služby Azure monitor](../service-limits.md#data-ingestion-volume-rate).

Zákazníci, kteří ingestují méně než 4 TB za den, obvykle tyto limity nebudou splňovat. Zákazníci, kteří ingestují větší objemy nebo mají špičky v rámci svých běžných operací, zvažte přechod na [vyhrazené clustery](../log-query/logs-dedicated-clusters.md) , u kterých by bylo možné vystavit omezení rychlosti příjmu.

Když je povolený limit přenosové rychlosti příjmu nebo získá 80% prahové hodnoty, přidá se do tabulky *Operations* v pracovním prostoru událost. Doporučuje se ho monitorovat a vytvořit výstrahu. Další podrobnosti najdete v tématu [frekvence zpracování objemu dat](../service-limits.md#data-ingestion-volume-rate).


## <a name="recommendations"></a>Doporučení

![Příklad návrhu kontextu prostředků](./media/design-logs-deployment/workspace-design-resource-context-01.png)

Tento scénář pokrývá jeden návrh pracovního prostoru v rámci předplatného IT organizace, které není omezené na základě suverenity dat nebo dodržování předpisů nebo které musí namapovat na oblasti, ve kterých jsou nasazené prostředky. Umožňuje týmům zabezpečení a správců IT ve vaší organizaci využít vylepšenou integraci se správou přístupu k Azure a bezpečnějším řízením přístupu.

Všechny prostředky, řešení monitorování a přehledy, jako Application Insights a Azure Monitor pro virtuální počítače, podporují infrastrukturu a aplikace udržované různými týmy, aby se jejich shromážděná data do centralizovaného sdíleného pracovního prostoru organizace IT nastavily. Uživatelům v každém týmu je udělen přístup k protokolům pro prostředky, kterým byl udělen přístup.

Jakmile nasadíte architekturu pracovního prostoru, můžete to vyhovět v prostředcích Azure pomocí [Azure Policy](../../governance/policy/overview.md). Poskytuje způsob, jak definovat zásady a zajistit kompatibilitu s prostředky Azure, aby odesílaly všechny své protokoly prostředků do konkrétního pracovního prostoru. Například u virtuálních počítačů Azure nebo služby Virtual Machine Scale Sets můžete použít existující zásady, které vyhodnocují dodržování předpisů v pracovním prostoru a výsledky sestav, nebo přizpůsobit pro nápravu, pokud nedodržují předpisy.  

## <a name="workspace-consolidation-migration-strategy"></a>Strategie migrace konsolidace v pracovním prostoru

Pro zákazníky, kteří už mají nasazené několik pracovních prostorů a mají zájem o konsolidaci do modelu přístupu kontextu prostředků, doporučujeme, abyste provedli přírůstkový přístup k migraci na doporučený model přístupu a nepokusili jste se ho rychle nebo agresivní. Po dvoufázovém přístupu k plánování, migraci, ověřování a vyřazení po přiměřené časové ose vám pomůže vyhnout se jakýmkoli neplánovaným incidentům nebo neočekávaným dopadům na cloudové operace. Pokud nemáte zásady uchovávání dat pro dodržování předpisů nebo obchodních důvodů, je nutné vyhodnotit příslušnou dobu, která bude uchovávat data v pracovním prostoru, ze kterého migrujete během procesu. Při překonfigurování prostředků k vytváření sestav do sdíleného pracovního prostoru můžete podle potřeby analyzovat data v původním pracovním prostoru. Pokud se po dokončení migrace rozhodnete zachovat data v původním pracovním prostoru před koncem doby uchovávání, neodstraňujte ji.

Při plánování migrace do tohoto modelu Vezměte v úvahu následující skutečnosti:

* Pochopte, jaké oborové předpisy a interní zásady týkající se uchovávání dat musíte dodržovat.
* Ujistěte se, že týmy vaší aplikace můžou pracovat v rámci stávajících funkcí kontextu prostředků.
* Identifikujte přístup k prostředkům pro týmy vaší aplikace a otestujte ve vývojovém prostředí před implementací v produkčním prostředí.
* Nakonfigurujte pracovní prostor tak, aby povoloval **používání oprávnění prostředku nebo pracovního prostoru**.
* Odebere aplikační týmy oprávnění ke čtení a dotazování pracovního prostoru.
* Povolte a nakonfigurujte jakákoli řešení monitorování, přehledy, jako je Azure Monitor pro kontejnery nebo Azure Monitor pro virtuální počítače, účty Automation a řešení pro správu, jako je například Update Management, spustit nebo zastavit virtuální počítače atd., které byly nasazeny v původním pracovním prostoru.

## <a name="next-steps"></a>Další kroky

Chcete-li implementovat oprávnění zabezpečení a ovládací prvky doporučené v této příručce, přečtěte si téma [Správa přístupu k protokolům](manage-access.md).