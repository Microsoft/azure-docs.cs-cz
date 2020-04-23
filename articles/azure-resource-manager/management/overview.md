---
title: Přehled
description: Popisuje, jak Azure Resource Manager využívat k nasazení, správě a řízení přístupu k prostředkům v Azure.
ms.topic: overview
ms.date: 04/21/2020
ms.openlocfilehash: 253fc2f296fa764a6c22fa1331221df60ca21bb5
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870491"
---
# <a name="what-is-azure-resource-manager"></a>Co je Správce prostředků Azure?

Azure Resource Manager je služba nasazování a správy pro Azure. Poskytuje vrstvu správy, která umožňuje vytvářet, aktualizovat a odstraňovat prostředky ve vašem účtu Azure. Funkce správy, jako je řízení přístupu, zámky a značky, slouží k zabezpečení a uspořádání prostředků po nasazení.

Další informace o šablonách Azure Resource Manageru najdete v [tématu Přehled nasazení šablony](../templates/overview.md).

## <a name="consistent-management-layer"></a>Konzistentní vrstva správy

Když uživatel odešle požadavek z některého z nástrojů Azure, rozhraní API nebo sad SDK, správce prostředků obdrží požadavek. Ověřuje a autorizuje požadavek. Správce prostředků odešle požadavek službě Azure, která provede požadovanou akci. Vzhledem k tomu, že všechny požadavky jsou zpracovávány prostřednictvím stejného rozhraní API, zobrazí konzistentní výsledky a možnosti ve všech různých nástrojích.

Následující obrázek ukazuje roli Azure Resource Manager hraje při zpracování požadavků Azure.

![Model požadavku Resource Manageru](./media/overview/consistent-management-layer.png)

Všechny funkce, které jsou k dispozici na portálu jsou k dispozici také prostřednictvím Prostředí PowerShell, Azure CLI, REST API a klientské sady SDK. Funkce původně vydané prostřednictvím rozhraní API budou na portálu k dispozici do 180 dnů od počátečního vydání.

## <a name="terminology"></a>Terminologie

Pokud s Azure Resource Managerem začínáte, existuje několik termínů, které možná neznáte.

* **prostředek** - Spravovatelná položka, která je k dispozici prostřednictvím služby Azure. Příkladem prostředků jsou virtuální počítače, účty úložiště, webové aplikace, databáze a virtuální sítě. Skupiny prostředků, předplatná, skupiny pro správu a značky jsou také příklady prostředků.
* **skupina prostředků** – kontejner, který obsahuje související prostředky pro řešení Azure. Skupina prostředků zahrnuje ty prostředky, které chcete spravovat jako skupinu. O tom, které prostředky do skupiny prostředků patří, rozhodujete vy na základě toho, co je pro vaši organizaci nejvhodnější. Viz [Skupiny prostředků](#resource-groups).
* **zprostředkovatel prostředků** – služba, která poskytuje prostředky Azure. Například běžným poskytovatelem prostředků je Microsoft.Compute, který poskytuje prostředek virtuálního počítače. Microsoft.Storage je další běžný poskytovatel prostředků. Viz [Zprostředkovatelé a typy prostředků](resource-providers-and-types.md).
* **Šablona Správce prostředků** – soubor JSON (JavaScript Object Notation), který definuje jeden nebo více prostředků pro nasazení do skupiny prostředků, předplatného, skupiny pro správu nebo klienta. Šablony lze použít k nasazení prostředků konzistentně a opakovaně. Viz [Přehled nasazení šablony](../templates/overview.md).
* **deklarativní syntaxe** – Syntaxe, která umožňuje prohlásit „Toto mám v úmyslu vytvořit“, aniž by k tomu bylo nutné psát sekvence programových příkazů. Šablona Resource Manageru je příkladem deklarativní syntaxe. V souboru definujete vlastnosti pro infrastrukturu k nasazení do Azure.  Viz [Přehled nasazení šablony](../templates/overview.md).

## <a name="the-benefits-of-using-resource-manager"></a>Výhody použití Resource Manageru

Pomocí Správce prostředků můžete:

* Spravujte infrastrukturu prostřednictvím deklarativních šablon, nikoli skriptů.

* Nasazujte, spravujte a monitorujte všechny prostředky pro vaše řešení jako skupinu, nikoli zpracování těchto prostředků jednotlivě.

* Znovu nasaďte své řešení v průběhu životního cyklu vývoje a mějte jistotu, že vaše prostředky jsou nasazeny v konzistentním stavu.

* Definujte závislosti mezi prostředky tak, aby byly nasazeny ve správném pořadí.

* Použít řízení přístupu pro všechny služby, protože řízení přístupu na základě rolí (RBAC) je nativně integrován do platformy pro správu.

* Použijte značky na prostředky logicky uspořádat všechny prostředky ve vašem předplatném.

* Vyjasněte fakturaci vaší organizace zobrazením nákladů pro skupinu prostředků sdílejících stejnou značku.

## <a name="understand-scope"></a>Orientace v oborech

Azure poskytuje čtyři úrovně oboru: [skupiny pro správu](../../governance/management-groups/overview.md), předplatná, [skupiny prostředků](#resource-groups)a prostředky. Následující obrázek ukazuje příklad těchto vrstev.

![Úrovně řízení](./media/overview/scope-levels.png)

Nastavení správy můžete použít na jakékoli z těchto úrovní rozsahu. Vybraná úroveň určuje rozsah použití nastavení. Nižší úrovně dědí nastavení z vyšších úrovní. Pokud například použijete [zásadu](../../governance/policy/overview.md) pro předplatné, zásady se použijí na všechny skupiny prostředků a prostředky ve vašem předplatném. Použijete-li zásadu pro skupinu prostředků, použije se tato zásada skupinu prostředků a všechny její prostředky. Jiná skupina prostředků však nemá toto přiřazení zásad.

Šablony můžete nasadit do tenantů, skupin pro správu, předplatných nebo skupin prostředků.

## <a name="resource-groups"></a>Skupiny prostředků

Při definování skupin prostředků byste měli vzít v úvahu některé důležité faktory:

* Všechny prostředky ve skupině by měly sdílet stejný životní cyklus. Nasazujete, aktualizujete a odstraňujete je společně. Pokud některý z prostředků, například databázový server, musí mít jiný cyklus nasazení, měl by být v jiné skupině prostředků.

* Každý prostředek může být jenom v jedné skupině prostředků.

* Některé prostředky mohou existovat mimo skupinu prostředků. Tyto prostředky se nasazují do [předplatného](../templates/deploy-to-subscription.md), [skupiny pro správu](../templates/deploy-to-management-group.md)nebo [klienta](../templates/deploy-to-tenant.md). V těchto oborech jsou podporovány pouze určité typy prostředků.

* Prostředky je možné do skupiny prostředků kdykoli přidat nebo naopak odebrat.

* Prostředky je možné přesouvat mezi skupinami. Další informace najdete v tématu, které se zabývá [přesunutím prostředků do nové skupiny prostředků nebo předplatného](move-resource-group-and-subscription.md).

* Skupina prostředků může obsahovat prostředky, které jsou umístěny v různých oblastech.

* Skupinu prostředků lze využít k určení rozsahu řízení přístupu pro akce správy.

* Prostředek může interagovat s prostředky v dalších skupinách prostředků. Tato interakce je běžná v případě, že spolu tyto dva prostředky souvisejí, ale nesdílejí stejný životní cyklus (například webové aplikace, které se připojují k databázi).

Při vytváření skupiny prostředků pro ni musíte zadat umístění. Asi vás zajímá, proč skupina prostředků potřebuje umístění. A proč vůbec záleží na umístění skupiny prostředků, pokud prostředky mohou mít jiná umístění než skupina prostředků. Skupina prostředků ukládá metadata o prostředcích. Když zadáte umístění pro skupinu prostředků, určujete, kde jsou tato metadata uložena. Z důvodu dodržování předpisů může být nutné zajistit, aby se data ukládala v určité oblasti.

Pokud je oblast skupiny prostředků dočasně nedostupná, nemůžete aktualizovat prostředky ve skupině prostředků, protože metadata nejsou k dispozici. Prostředky v jiných oblastech budou stále fungovat podle očekávání, ale nelze je aktualizovat. Další informace o vytváření spolehlivých aplikací najdete v [tématu Navrhování spolehlivých aplikací Azure](/azure/architecture/checklist/resiliency-per-service).

## <a name="resiliency-of-azure-resource-manager"></a>Odolnost správce prostředků Azure

Služba Azure Resource Manager je navržená pro odolnost proti chybám a nepřetržitou dostupnost. Operace Správce prostředků a řízení roviny (požadavky odeslané management.azure.com) v rozhraní REST API jsou:

* Distribuováno mezi regiony. Některé služby jsou regionální.

* Distribuovány mezi zónami dostupnosti (také oblasti) v umístěních, které mají více zón dostupnosti.

* Není závislá na jednom logickém datovém centru.

* Nikdy se neschytána kvůli údržbě.

Tato odolnost proti chybám platí pro služby, které přijímají požadavky prostřednictvím Správce prostředků. Například trezor klíčů těží z této odolnosti proti chybám.

## <a name="next-steps"></a>Další kroky

* Další informace o přesunutí prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](move-resource-group-and-subscription.md).

* Další informace o označování prostředků najdete [v tématu Použití značek k uspořádání prostředků Azure](tag-resources.md).

* Další informace o uzamčení prostředků naleznete v [tématu Uzamčení prostředků, abyste předešli neočekávaným změnám](lock-resources.md).
