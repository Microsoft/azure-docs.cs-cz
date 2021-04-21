---
title: Bicep jazyk pro šablony Azure Resource Manager
description: Popisuje jazyk bicep pro nasazení infrastruktury do Azure prostřednictvím šablon Azure Resource Manager.
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: af207e6ca88eab50fe6030883379c87c0ec05691
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773742"
---
# <a name="what-is-bicep-preview"></a>Co je bicep (Preview)?

Bicep je jazyk pro deklarativní nasazení prostředků Azure. K vývoji šablon Azure Resource Manager (šablon ARM) můžete použít bicep místo JSON. Bicep zjednodušuje prostředí pro vytváření, protože poskytuje stručnou syntaxi, lepší podporu pro opětovné použití kódu a lepší bezpečnost typů. Bicep je jazyk specifický pro doménu (DSL), což znamená, že je navržený pro konkrétní scénář nebo doménu. Není určený jako obecný programovací jazyk pro psaní aplikací.

Syntaxe JSON pro vytvoření šablony může být podrobná a vyžaduje složitý výraz. Bicep vylepšuje toto prostředí bez ztráty funkcí šablony JSON. Jedná se o transparentní abstrakci přes JSON pro šablony ARM. Každý soubor bicep se zkompiluje na standardní šablonu ARM. Typy prostředků, verze rozhraní API a vlastnosti, které jsou platné v šabloně ARM, jsou platné v souboru bicep. Aktuální verze obsahuje několik [známých omezení](#known-limitations) .

Bicep je aktuálně ve verzi Preview. Chcete-li sledovat stav práce, přečtěte si téma [úložiště projektu bicep](https://github.com/Azure/bicep).

Další informace o bicep najdete v následujícím videu.

> [!VIDEO https://www.youtube.com/embed/sc1kJfcRQgY]

## <a name="get-started"></a>Začínáme

Pokud chcete začít s bicep, [nainstalujte nástroje](bicep-install.md).

Po instalaci nástrojů Vyzkoušejte [kurz bicep](./bicep-tutorial-create-first-bicep.md). Série kurzů vás provede strukturou a funkcemi bicep. Nasadíte soubory bicep a převedete šablonu ARM na ekvivalentní soubor bicep.

Chcete-li zobrazit ekvivalentní soubory JSON a bicep vedle sebe, přečtěte si téma [bicep Playground](https://aka.ms/bicepdemo).

Pokud máte existující šablonu ARM, kterou byste chtěli převést na bicep, přečtěte si téma [Převod šablon ARM mezi JSON a bicep](bicep-decompile.md).

## <a name="benefits-of-bicep-versus-other-tools"></a>Výhody bicep oproti jiným nástrojům

Bicep poskytuje následující výhody oproti jiným možnostem:

* **Podpora pro všechny typy prostředků a verze rozhraní API**: bicep okamžitě podporuje všechny verze Preview a GA pro služby Azure. Jakmile poskytovatel prostředků zavádí nové typy prostředků a verze rozhraní API, můžete je použít v souboru bicep. Než začnete používat nové služby, nemusíte čekat na aktualizaci nástrojů.
* **Prostředí pro vytváření obsahu**: při použití vs Code k vytvoření souborů bicep získáte prostředí pro vytváření první třídy. Editor poskytuje bohatou bezpečnost typů, technologie IntelliSense a ověřování syntaxe.
* **Modularita**: kód bicep můžete rozdělit na spravovatelné části pomocí [modulů](bicep-modules.md). Modul nasadí sadu souvisejících prostředků. Moduly umožňují opakované použití kódu a zjednodušení vývoje. Modul můžete přidat do souboru bicep kdykoli, když potřebujete tyto prostředky nasadit.
* **Integrace se službami Azure**: bicep se integruje se službami Azure, jako jsou Azure Policy, specifikace šablon a plány.
* **Žádné soubory stavu nebo stavu ke správě**: veškerý stav je uložený v Azure. Uživatelé můžou spolupracovat a mít jistotu, že se jejich aktualizace zpracovávají podle očekávání. Chcete-li zobrazit náhled změn před nasazením šablony, použijte [operaci citlivostní zpracování](template-deploy-what-if.md) .
* **Bez nákladů a open source**: bicep je zcela zdarma. Nemusíte platit za prémiové funkce. Podporuje ho i podpora Microsoftu.

## <a name="bicep-improvements"></a>Vylepšení bicep

Bicep nabízí snadnější a přesnější syntaxi v porovnání s ekvivalentním JSON. Nepoužíváte `[...]` výrazy. Místo toho přímo voláte funkce a získáme hodnoty z parametrů a proměnných. Každému nasazenému prostředku udělíte symbolický název, který usnadňuje odkazování na prostředek ve vaší šabloně.

Například následující JSON vrátí výstupní hodnotu z vlastnosti prostředku.

```json
"outputs": {
  "hostname": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
    },
}
```

Ekvivalentní výstupní výraz v bicep je snazší psát. Následující příklad vrátí stejnou vlastnost pomocí symbolického názvu **publicIP** pro prostředek, který je definován v rámci šablony:

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

Úplné porovnání syntaxe naleznete v tématu [porovnání formátu JSON a bicep pro šablony](compare-template-syntax.md).

Bicep automaticky spravuje závislosti mezi prostředky. Můžete se vyhnout nastavení, `dependsOn` když se symbolický název prostředku používá v jiné deklaraci prostředků.

Struktura souboru bicep je flexibilnější než šablona JSON. Můžete deklarovat parametry, proměnné a výstupy kdekoli v souboru. Ve formátu JSON musíte deklarovat všechny parametry, proměnné a výstupy v odpovídajících částech šablony.

## <a name="known-limitations"></a>Známá omezení

V současné době existují následující omezení:

* Nejde nastavit režim nebo velikost dávky u smyček kopírování.
* Nejde kombinovat smyčky a podmínky.
* Jednořádkové objekty a pole, jako `['a', 'b', 'c']` , nejsou podporovány.

## <a name="faq"></a>Časté otázky

**Proč vytvořit nový jazyk namísto použití existujícího jazyka?**

Bicep můžete představit jako revizi existujícího jazyka šablon ARM, a ne pomocí nového jazyka. Syntaxe se změnila, ale základní funkce a modul runtime zůstávají stejné.

Před vývojem bicep se považujeme za použití stávajícího programovacího jazyka. Rozhodli jsme se, že naše cílová skupina by usnadnila seznámení s bicep, spíše než Začínáme s jiným jazykem.

**Proč se vám nezaměřuje vaše energie na Terraformu nebo jiné infrastruktury třetích stran jako nabídky kódu?**

Různí uživatelé dávají přednost různým jazykům a nástrojům konfigurace. Chceme zajistit, aby všechny tyto nástroje poskytovaly Skvělé prostředí v Azure. Bicep je součástí tohoto úsilí.

Pokud máte s Terraformu spokojeni, neexistuje žádný důvod k přepnutí. Společnost Microsoft se zavazuje, že bude mít jistotu, že se Terraformu na Azure nejlépe stane.

Pro zákazníky, kteří mají vybrané šablony ARM, se domníváme, že bicep vylepšuje prostředí pro vytváření obsahu. Bicep také pomáhá s přechodem pro zákazníky, kteří nepřijali infrastrukturu jako kód.

**Je bicep jenom pro Azure?**

Bicep je DSL zaměřená na nasazení kompletních řešení do Azure. Splnění tohoto cíle vyžaduje práci s některými rozhraními API, která jsou mimo Azure. Očekáváme, že pro tyto scénáře budeme poskytovat body rozšiřitelnosti.

**Co se stane s existujícími šablonami ARM?**

Stále fungují přesně tak, jak mají. Nemusíte dělat žádné změny. Budeme dál podporovat základní jazyk JSON šablony ARM. Soubory bicep se zkompiluje do formátu JSON a tento JSON se pošle do Azure pro nasazení.

Až budete připraveni, můžete [soubory JSON převést na bicep](bicep-decompile.md).

## <a name="next-steps"></a>Další kroky

Začněte s [kurzem k bicep](./bicep-tutorial-create-first-bicep.md).
