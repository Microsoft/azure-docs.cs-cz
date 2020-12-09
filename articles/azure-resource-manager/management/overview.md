---
title: Přehled Azure Resource Manageru
description: Popisuje, jak Azure Resource Manager využívat k nasazení, správě a řízení přístupu k prostředkům v Azure.
ms.topic: overview
ms.date: 09/01/2020
ms.custom: contperfq1
ms.openlocfilehash: 317d7cae9c0febcc99831b20525b9d9d2cc96651
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96903186"
---
# <a name="what-is-azure-resource-manager"></a>Co je Azure Resource Manager?

Azure Resource Manager je služba nasazování a správy pro Azure. Poskytuje úroveň správy, která vám umožňuje vytvářet, aktualizovat a odstraňovat prostředky v účtu Azure. Pomocí funkcí správy, jako jsou řízení přístupu, zámky a značky, můžete zabezpečit a organizovat prostředky po nasazení.

Další informace o šablonách Azure Resource Manager (šablonách ARM) najdete v tématu [Přehled nasazení šablon](../templates/overview.md).

## <a name="consistent-management-layer"></a>Konzistentní vrstva správy

Když uživatel odešle z jakéhokoli nástroje Azure, rozhraní API nebo sady SDK požadavek, obdrží ho služba Resource Manager. Požadavek ověří a autorizuje. Resource Manager odešle požadavek do služby Azure, která provede požadovanou akci. Vzhledem k tomu, že se všechny požadavky zpracovávají přes stejné rozhraní API, zobrazí se konzistentní výsledky a možnosti ve všech různých nástrojích.

Následující obrázek ukazuje, jakou roli hraje Azure Resource Manager při zpracování požadavků Azure.

![Model požadavku Resource Manageru](./media/overview/consistent-management-layer.png)

Všechny funkce, které jsou k dispozici na portálu, jsou také dostupné prostřednictvím PowerShellu, rozhraní Azure CLI, rozhraní REST API a klientských sad SDK. Funkce původně vydané prostřednictvím rozhraní API budou na portálu k dispozici do 180 dnů od počátečního vydání.

## <a name="terminology"></a>Terminologie

Pokud s Azure Resource Managerem začínáte, existuje několik termínů, které možná neznáte.

* **prostředek** – spravovatelná položka, která je dostupná prostřednictvím Azure. Mezi příklady prostředků patří virtuální počítače, účty úložiště, webové aplikace, databáze a virtuální sítě. Příklady prostředků jsou také skupiny prostředků, předplatná, skupiny pro správu a značky.
* **Skupina prostředků** – kontejner, který obsahuje související prostředky pro řešení Azure. Skupina prostředků zahrnuje ty prostředky, které chcete spravovat jako skupinu. O tom, které prostředky do skupiny prostředků patří, rozhodujete vy na základě toho, co je pro vaši organizaci nejvhodnější. Viz [Skupiny prostředků](#resource-groups).
* **poskytovatel prostředků** – služba poskytující prostředky Azure. Například běžný poskytovatel prostředků je Microsoft. COMPUTE, který poskytuje prostředek virtuálního počítače. Microsoft. Storage je další společný poskytovatel prostředků. Viz téma [poskytovatelé a typy prostředků](resource-providers-and-types.md).
* **Správce prostředků šablonu** – soubor JavaScript Object Notation (JSON), který definuje jeden nebo více prostředků pro nasazení do skupiny prostředků, předplatného, skupiny pro správu nebo tenanta. Šablony lze použít k nasazení prostředků konzistentně a opakovaně. Další informace najdete v tématu [přehled Template Deployment](../templates/overview.md).
* **deklarativní syntaxe** – Syntaxe, která umožňuje prohlásit „Toto mám v úmyslu vytvořit“, aniž by k tomu bylo nutné psát sekvence programových příkazů. Šablona Resource Manageru je příkladem deklarativní syntaxe. V souboru definujete vlastnosti pro infrastrukturu k nasazení do Azure.  Další informace najdete v tématu [přehled Template Deployment](../templates/overview.md).

## <a name="the-benefits-of-using-resource-manager"></a>Výhody použití Resource Manageru

Pomocí Resource Manageru můžete:

* Spravovat infrastrukturu prostřednictvím deklarativních šablon místo skriptů.

* Nasadit, spravovat a sledovat veškeré prostředky pro vaše řešení jako skupinu, nemusíte s nimi pracovat samostatně.

* Opětovně nasadit řešení v celém průběhu vývojového životního cyklu a mít jistotu, že se vaše prostředky nasazují konzistentně.

* Definovat závislosti mezi prostředky, aby se nasazovaly ve správném pořadí.

* Použijte řízení přístupu pro všechny služby, protože řízení přístupu na základě role Azure (Azure RBAC) je nativně integrované do platformy pro správu.

* Používat značky pro prostředky, abyste logicky uspořádali všechny prostředky ve vašem předplatném.

* Objasnit si fakturaci organizace zobrazením nákladů za skupinu prostředků sdílejících stejnou značku.

## <a name="understand-scope"></a>Orientace v oborech

Azure poskytuje čtyři úrovně rozsahu: [skupiny pro správu](../../governance/management-groups/overview.md), předplatná, [skupiny prostředků](#resource-groups)a prostředky. Následující obrázek ukazuje příklad těchto vrstev.

![Úrovně správy](./media/overview/scope-levels.png)

Nastavení správy můžete použít na jakékoli z těchto úrovní rozsahu. Vybraná úroveň určuje rozsah použití nastavení. Nižší úrovně dědí nastavení z vyšších úrovní. Když například použijete [zásadu](../../governance/policy/overview.md) pro předplatné, zásada se použije na všechny skupiny prostředků a prostředky v rámci vašeho předplatného. Když použijete zásadu pro skupinu prostředků, tato zásada se použije pro skupinu prostředků a všechny její prostředky. U jiné skupiny prostředků ale tato přiřazení zásad neplatí.

Šablony můžete nasadit do klientů, skupin pro správu, předplatných nebo skupin prostředků.

## <a name="resource-groups"></a>Skupiny prostředků

Při definování skupin prostředků byste měli vzít v úvahu některé důležité faktory:

* Všechny prostředky ve vaší skupině prostředků by měly sdílet stejný životní cyklus. Nasazujete, aktualizujete a odstraňujete je společně. Pokud jeden prostředek, třeba server, musí existovat v jiném cyklu nasazení, měl by být v jiné skupině prostředků.

* Každý prostředek může existovat jen v jedné skupině prostředků.

* Prostředky je možné do skupiny prostředků kdykoli přidat nebo naopak odebrat.

* Prostředky je možné přesouvat mezi skupinami. Další informace najdete v tématu, které se zabývá [přesunutím prostředků do nové skupiny prostředků nebo předplatného](move-resource-group-and-subscription.md).

* Prostředky ve skupině prostředků se můžou nacházet v různých oblastech, než je skupina prostředků.

* Při vytváření skupiny prostředků pro ni musíte zadat umístění. Asi vás zajímá, proč skupina prostředků potřebuje umístění. A proč vůbec záleží na umístění skupiny prostředků, pokud prostředky mohou mít jiná umístění než skupina prostředků. Skupina prostředků ukládá metadata o prostředcích. Když zadáte umístění pro skupinu prostředků, určíte, kde jsou tato metadata uložená. Z důvodu dodržování předpisů může být nutné zajistit, aby se data ukládala v určité oblasti.

   Pokud je oblast skupiny prostředků dočasně nedostupná, nemůžete aktualizovat prostředky ve skupině prostředků, protože metadata nejsou k dispozici. Prostředky v jiných oblastech budou pořád fungovat podle očekávání, ale nemůžete je aktualizovat. Další informace o vytváření spolehlivých aplikací najdete v tématu [navrhování spolehlivých aplikací Azure](/azure/architecture/checklist/resiliency-per-service).

* Skupinu prostředků lze využít k určení rozsahu řízení přístupu pro akce správy. Pokud chcete spravovat skupinu prostředků, můžete přiřadit [zásady Azure](../../governance/policy/overview.md), [role Azure](../../role-based-access-control/role-assignments-portal.md)nebo [zámky prostředků](lock-resources.md).

* Značky můžete [použít](tag-resources.md) pro skupinu prostředků. Prostředky ve skupině prostředků tyto značky nedědí.

* Prostředek se může připojit k prostředkům v jiných skupinách prostředků. Tento scénář je běžný v případě, že tyto dva prostředky souvisejí, ale nesdílejí stejný životní cyklus. Můžete mít například webovou aplikaci, která se připojuje k databázi v jiné skupině prostředků.

* Při odstranění skupiny prostředků se odstraní také všechny prostředky ve skupině prostředků. Informace o tom, jak Azure Resource Manager orchestruje tato odstranění, najdete v tématu [Azure Resource Manager skupiny prostředků a odstraňování prostředků](delete-resource-group.md).

* V každé skupině prostředků můžete nasadit až 800 instancí typu prostředku. Některé typy prostředků jsou [vyloučené z limitu instancí 800](resources-without-resource-group-limit.md).

* Některé prostředky můžou existovat mimo skupinu prostředků. Tyto prostředky se nasazují do [předplatného](../templates/deploy-to-subscription.md), [skupiny pro správu](../templates/deploy-to-management-group.md)nebo [tenanta](../templates/deploy-to-tenant.md). V těchto oborech jsou podporovány pouze konkrétní typy prostředků.

* Pokud chcete vytvořit skupinu prostředků, můžete použít [portál](manage-resource-groups-portal.md#create-resource-groups), [PowerShell](manage-resource-groups-powershell.md#create-resource-groups), rozhraní příkazového [řádku Azure](manage-resource-groups-cli.md#create-resource-groups)nebo [šablonu ARM](../templates/deploy-to-subscription.md#resource-groups).

## <a name="resiliency-of-azure-resource-manager"></a>Odolnost Azure Resource Manager

Služba Azure Resource Manager je navržena pro zajištění odolnosti a nepřetržité dostupnosti. Správce prostředků a řídící operace roviny (požadavky odeslané na management.azure.com) v REST API jsou:

* Distribuované napříč oblastmi. Některé služby jsou regionální.

* Distribuováno mezi Zóny dostupnosti (jako v oblastech) v umístěních, která mají více Zóny dostupnosti.

* Nezávislá na jednom logickém datovém centru.

* Pro aktivity údržby se nikdy neukončí.

Tato odolnost se vztahuje na služby, které přijímají požadavky prostřednictvím Správce prostředků. Například Key Vault výhody z této odolnosti.

## <a name="next-steps"></a>Další kroky

* Další informace o přesouvání prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](move-resource-group-and-subscription.md).

* Další informace o označování prostředků najdete v tématu [použití značek k uspořádání prostředků Azure](tag-resources.md).

* Další informace o uzamykání prostředků najdete v tématu [uzamčení prostředků, aby nedocházelo k neočekávaným změnám](lock-resources.md).
