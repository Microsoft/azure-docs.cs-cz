---
title: Bicep jazyk pro šablony Azure Resource Manager
description: Popisuje jazyk bicep pro nasazení infrastruktury do Azure prostřednictvím šablon Azure Resource Manager.
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 6f706081d04c5337bb3def8088fe97b08c814ea2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594219"
---
# <a name="what-is-bicep-preview"></a>Co je bicep (Preview)?

Bicep je jazyk pro deklarativní nasazení prostředků Azure. K vývoji šablon Azure Resource Manager (šablon ARM) můžete použít bicep místo JSON. Bicep zjednodušuje prostředí pro vytváření, protože poskytuje stručnou syntaxi, lepší podporu pro opětovné použití kódu a lepší bezpečnost typů. Bicep je jazyk specifický pro doménu (DSL), což znamená, že je navržený pro konkrétní scénář nebo doménu. Není určený jako obecný programovací jazyk pro psaní aplikací.

Syntaxe JSON pro vytvoření šablony může být podrobná a vyžaduje složitý výraz. Bicep vylepšuje toto prostředí bez ztráty funkcí šablony JSON. Jedná se o transparentní abstrakci přes JSON pro šablony ARM. Každý soubor bicep se zkompiluje na standardní šablonu ARM. Typy prostředků, verze rozhraní API a vlastnosti, které jsou platné v šabloně ARM, jsou platné v souboru bicep. Aktuální verze obsahuje několik [známých omezení](#known-limitations) .

Další informace o bicep najdete v následujícím videu.

> [!VIDEO https://mediusprodstatic.studios.ms/asset-cccfdaf2-cdbe-49dd-9c58-91a4fe5ff0fd/OD340_1920x1080_AACAudio_5429.mp4?sv=2018-03-28&sr=b&sig=N3DuBaTrK3nt5TGwIagTbCqjVrzgwiJ9at80MXQJFwg%3D&st=2021-03-02T01%3A22%3A57Z&se=2026-03-02T01%3A27%3A57Z&sp=r&rscd=filename%3DIGFY21Q3-OD340-Learn%2Beverything%2Babout%2Bthe%2Bnext%2Bgeneration%2Bof%2BARM.mp4]

## <a name="get-started"></a>Začínáme

Pokud chcete začít s bicep, [nainstalujte nástroje](https://github.com/Azure/bicep/blob/main/docs/installing.md).

Po instalaci nástrojů Vyzkoušejte [kurz bicep](./bicep-tutorial-create-first-bicep.md). Série kurzů vás provede strukturou a funkcemi bicep. Nasadíte soubory bicep a převedete šablonu ARM na ekvivalentní soubor bicep.

Chcete-li zobrazit ekvivalentní soubory JSON a bicep vedle sebe, přečtěte si téma [bicep Playground](https://aka.ms/bicepdemo).

Pokud máte existující šablonu ARM, kterou byste chtěli převést na bicep, přečtěte si téma [Převod šablon ARM mezi JSON a bicep](bicep-decompile.md).

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

Rozšíření VS Code pro bicep nabízí bohatou validaci a technologii IntelliSense. Například můžete použít technologii IntelliSense rozšíření pro získání vlastností prostředku.

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
