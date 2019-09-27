---
title: Přehled Azure Resource Manageru | Dokumentace Microsoftu
description: Popisuje, jak Azure Resource Manager využívat k nasazení, správě a řízení přístupu k prostředkům v Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: overview
ms.date: 08/29/2019
ms.author: tomfitz
ms.openlocfilehash: f8ec833a469e3c3a9b4c88343a48e109fc5c9728
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338211"
---
# <a name="azure-resource-manager-overview"></a>Přehled Azure Resource Manageru

Azure Resource Manager je služba pro nasazení a správu pro Azure. Poskytuje vrstvu pro správu, která umožňuje vytvářet, aktualizovat a odstraňovat prostředky v předplatném Azure. Pomocí funkcí správy, jako jsou řízení přístupu, zámky a značky, můžete zabezpečit a organizovat prostředky po nasazení.

Další informace o šablonách Azure Resource Manager najdete v tématu [template Deployment Overview](template-deployment-overview.md).

## <a name="consistent-management-layer"></a>Konzistentní vrstva správy

Když uživatel odešle žádost ze všech nástrojů, rozhraní API nebo sad Azure, Správce prostředků obdrží požadavek. Ověřuje a autorizuje požadavek. Správce prostředků odešle požadavek službě Azure, která provede požadovanou akci. Vzhledem k tomu, že všechny požadavky jsou zpracovávány přes stejné rozhraní API, zobrazí se konzistentní výsledky a možnosti ve všech různých nástrojích.

Následující obrázek ukazuje, Azure Resource Manager role hraje při zpracování požadavků Azure. 

![Model požadavku Resource Manageru](./media/resource-group-overview/consistent-management-layer.png)

Všechny funkce, které jsou k dispozici na portálu, jsou také dostupné prostřednictvím PowerShellu, rozhraní příkazového řádku Azure CLI, rozhraní REST API a klientských sad SDK. Funkce původně vydané prostřednictvím rozhraní API budou na portálu k dispozici do 180 dnů od počátečního vydání.

## <a name="terminology"></a>Terminologie

Pokud s Azure Resource Managerem začínáte, existuje několik termínů, které možná neznáte.

* **prostředek** - Spravovatelná položka, která je k dispozici prostřednictvím služby Azure. Příklady prostředků jsou virtuální počítače, účty úložiště, webové aplikace, databáze a virtuální sítě.
* **skupina prostředků** – Kontejner, který obsahuje související prostředky pro řešení Azure. Skupina prostředků zahrnuje prostředky, které chcete spravovat jako skupinu. Rozhodnete, které prostředky patří do skupiny prostředků na základě toho, co je pro vaši organizaci nejvhodnější. Viz [Skupiny prostředků](#resource-groups).
* **poskytovatel prostředků** – služba poskytující prostředky Azure. Například běžný poskytovatel prostředků je Microsoft. COMPUTE, který poskytuje prostředek virtuálního počítače. Microsoft. Storage je další společný poskytovatel prostředků. Viz téma [poskytovatelé a typy prostředků](resource-manager-supported-services.md).
* **Správce prostředků šablona** – soubor JavaScript Object Notation (JSON), který definuje jeden nebo více prostředků pro nasazení do skupiny prostředků nebo předplatného. Šablony lze použít k nasazení prostředků konzistentně a opakovaně. Další informace najdete v tématu [přehled Template Deployment](template-deployment-overview.md).
* **deklarativní syntaxe** – Syntaxe, která umožňuje prohlásit „Toto mám v úmyslu vytvořit“, aniž by k tomu bylo nutné psát sekvence programových příkazů. Šablona Resource Manageru je příkladem deklarativní syntaxe. V souboru definujete vlastnosti pro infrastrukturu k nasazení do Azure.  Další informace najdete v tématu [přehled Template Deployment](template-deployment-overview.md).

## <a name="the-benefits-of-using-resource-manager"></a>Výhody použití Resource Manageru

Pomocí Správce prostředků můžete:

* Spravujte svoji infrastrukturu prostřednictvím deklarativních šablon místo skriptů.

* Nasaďte, spravujte a monitorujte všechny prostředky pro vaše řešení jako skupinu, místo toho, aby se tyto prostředky nemusely zpracovávat jednotlivě.

* Znovu nasaďte řešení v průběhu životního cyklu vývoje a měli byste mít jistotu, že se prostředky nasazují v konzistentním stavu.

* Definujte závislosti mezi prostředky tak, aby byly nasazeny ve správném pořadí.

* Použijte řízení přístupu pro všechny služby ve vaší skupině prostředků, protože Access Control na základě rolí je nativně integrovaná do platformy pro správu.

* Použijte značky pro prostředky k logickému uspořádání všech prostředků v rámci vašeho předplatného.

* Vyjasnění fakturace vaší organizace zobrazením nákladů na skupinu prostředků, které sdílejí stejnou značku.

## <a name="understand-scope"></a>Orientace v oborech

Azure poskytuje čtyři úrovně rozsahu: [skupiny pro správu](../governance/management-groups/overview.md), předplatná, [skupiny prostředků](#resource-groups)a prostředky. Následující obrázek ukazuje příklad těchto vrstev.

![Scope](./media/resource-group-overview/scope-levels.png)

Nastavení správy můžete použít na jakékoli z těchto úrovní rozsahu. Vybraná úroveň určuje rozsah použití nastavení. Nižší úrovně dědí nastavení z vyšších úrovní. Když například použijete [zásadu](../governance/policy/overview.md) pro předplatné, zásada se použije na všechny skupiny prostředků a prostředky v rámci vašeho předplatného. Když použijete zásadu pro skupinu prostředků, tato zásada se použije pro skupinu prostředků a všechny její prostředky. U jiné skupiny prostředků ale tato přiřazení zásad neplatí.

Šablony můžete nasadit do skupin pro správu, předplatných nebo skupin prostředků.

## <a name="resource-groups"></a>Skupiny prostředků

Při definování skupin prostředků byste měli vzít v úvahu některé důležité faktory:

* Všechny prostředky ve skupině by měly sdílet stejný životní cyklus. Nasazujete, aktualizujete a odstraňujete je společně. Pokud některý z prostředků, například databázový server, musí mít jiný cyklus nasazení, měl by být v jiné skupině prostředků.

* Každý prostředek může být jenom v jedné skupině prostředků.

* Prostředky je možné do skupiny prostředků kdykoli přidat nebo naopak odebrat.

* Prostředky je možné přesouvat mezi skupinami. Další informace najdete v tématu, které se zabývá [přesunutím prostředků do nové skupiny prostředků nebo předplatného](resource-group-move-resources.md).

* Skupina prostředků může obsahovat prostředky, které se nacházejí v různých oblastech.

* Skupinu prostředků lze využít k určení rozsahu řízení přístupu pro akce správy.

* Prostředek může interagovat s prostředky v dalších skupinách prostředků. Tato interakce je běžná v případě, že spolu tyto dva prostředky souvisejí, ale nesdílejí stejný životní cyklus (například webové aplikace, které se připojují k databázi).

Při vytváření skupiny prostředků pro ni musíte zadat umístění. Asi vás zajímá, proč skupina prostředků potřebuje umístění. A proč vůbec záleží na umístění skupiny prostředků, pokud prostředky mohou mít jiná umístění než skupina prostředků. Skupina prostředků ukládá metadata o prostředcích. Když zadáte umístění pro skupinu prostředků, určíte, kde jsou tato metadata uložená. Z důvodu dodržování předpisů může být nutné zajistit, aby se data ukládala v určité oblasti.

Pokud je oblast skupiny prostředků dočasně nedostupná, nemůžete aktualizovat prostředky ve skupině prostředků, protože metadata nejsou k dispozici. Prostředky v jiných oblastech budou pořád fungovat podle očekávání, ale nemůžete je aktualizovat. Další informace o vytváření spolehlivých aplikací najdete v tématu [navrhování spolehlivých aplikací Azure](/azure/architecture/reliability/).

## <a name="resiliency-of-azure-resource-manager"></a>Odolnost Azure Resource Manager

Služba Azure Resource Manager je navržena pro zajištění odolnosti a nepřetržité dostupnosti. Správce prostředků a řídící operace roviny (požadavky odeslané na management.azure.com) v REST API jsou:

* Distribuované napříč oblastmi. Některé služby jsou regionální.

* Distribuováno mezi Zóny dostupnosti (jako v oblastech) v umístěních, která mají více Zóny dostupnosti.

* Nezávislá na jednom logickém datovém centru.

* Pro aktivity údržby se nikdy neukončí.

Tato odolnost se vztahuje na služby, které přijímají požadavky prostřednictvím Správce prostředků. Například Key Vault výhody z této odolnosti.

## <a name="next-steps"></a>Další kroky

* Informace o všech operacích nabízených poskytovateli prostředků najdete v tématu [rozhraní API Azure REST](/rest/api/azure/).

* Další informace o přesouvání prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo](resource-group-move-resources.md)předplatného.

* Další informace o označování prostředků najdete v tématu [použití značek k uspořádání prostředků Azure](resource-group-using-tags.md).

* Další informace o uzamykání prostředků najdete v tématu [uzamčení prostředků, aby nedocházelo k neočekávaným změnám](resource-group-lock-resources.md).

* Informace o vytváření šablon pro nasazení najdete v tématu [template Deployment Overview](template-deployment-overview.md).
