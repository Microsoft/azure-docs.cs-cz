---
title: Bicep jazyk pro šablony Azure Resource Manager
description: Popisuje jazyk bicep pro nasazení infrastruktury do Azure prostřednictvím šablon Azure Resource Manager.
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 2fb13bca9e9d456889185d512ee2fc9d4cbbe673
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036380"
---
# <a name="what-is-bicep-preview"></a>Co je bicep (Preview)?

Bicep je jazyk pro deklarativní nasazení prostředků Azure. Zjednodušuje prostředí pro vytváření obsahu tím, že poskytuje stručnou syntaxi a lepší podporu pro opětovné použití kódu. Bicep je jazyk specifický pro doménu (DSL), což znamená, že je navržený pro konkrétní scénář nebo doménu. Bicep není určený jako obecný programovací jazyk pro psaní aplikací.

V minulosti jste vyvinuli Azure Resource Manager šablon (šablony ARM) pomocí formátu JSON. Syntaxe JSON pro vytvoření šablony může být podrobná a vyžaduje složitý výraz. Bicep vylepšuje toto prostředí bez ztráty funkcí šablony JSON. Jedná se o transparentní abstrakci přes JSON pro šablony ARM. Každý soubor bicep se zkompiluje na standardní šablonu ARM. Typy prostředků, verze rozhraní API a vlastnosti, které jsou platné v šabloně ARM, jsou platné v souboru bicep.

## <a name="get-started"></a>Začínáme

Pokud chcete začít s bicep, [nainstalujte nástroje](https://github.com/Azure/bicep/blob/main/docs/installing.md).

Po instalaci nástrojů Vyzkoušejte [kurz bicep](./bicep-tutorial-create-first-bicep.md). Série kurzů vás provede strukturou a funkcemi bicep. Nasadíte soubory bicep a převedete šablonu ARM na ekvivalentní soubor bicep.

Chcete-li zobrazit ekvivalentní soubory JSON a bicep vedle sebe, přečtěte si téma [bicep Playground](https://aka.ms/bicepdemo).

Pokud máte existující šablonu ARM, kterou byste chtěli převést na bicep, přečtěte si téma [dekompilace JSON do bicep](compare-template-syntax.md#decompile-json-to-bicep).

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

Pomocí bicep můžete přerušit projekt do více modulů.

Struktura souboru bicep je flexibilnější než šablona JSON. Můžete deklarovat parametry, proměnné a výstupy kdekoli v souboru. Ve formátu JSON musíte deklarovat všechny parametry, proměnné a výstupy v odpovídajících částech šablony.

Rozšíření VS Code pro bicep nabízí bohatší ověřování a IntelliSense. Například rozšíření má IntelliSense pro získání vlastností prostředku.

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

Až budete připraveni, můžete [soubory JSON převést na bicep](compare-template-syntax.md#decompile-json-to-bicep).

## <a name="next-steps"></a>Další kroky

Začněte s [kurzem k bicep](./bicep-tutorial-create-first-bicep.md).
