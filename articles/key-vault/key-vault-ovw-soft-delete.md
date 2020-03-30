---
title: Obnovitelné odstranění azure key vaultu | Dokumenty společnosti Microsoft
description: Obnovitelné odstranění v azure key vault umožňuje obnovit odstraněné trezory klíčů a objekty trezoru klíčů, jako jsou klíče, tajné klíče a certifikáty.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 03/19/2019
ms.openlocfilehash: 9c72b2ea71da94215fc9193ffdf3906449ec5571
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79457368"
---
# <a name="azure-key-vault-soft-delete-overview"></a>Přehled obnovitelného odstranění ve službě Azure Key Vault

Funkce obnovitelného odstranění trezoru klíčů umožňuje obnovení odstraněných trezorů a objektů úschovny, známých jako obnovitelné odstranění. Konkrétně se zabýváme následujícími scénáři:

- Podpora obnovitelného odstranění trezoru klíčů
- Podpora obnovitelného odstranění objektů trezoru klíčů (např. klíče, tajné klíče, certifikáty)

## <a name="supporting-interfaces"></a>Podpůrná rozhraní

Funkce obnovitelného odstranění je zpočátku k dispozici prostřednictvím rozhraní [REST](/rest/api/keyvault/), [CLI](key-vault-soft-delete-cli.md), [PowerShell](key-vault-soft-delete-powershell.md) a [.NET/C#.](/dotnet/api/microsoft.azure.keyvault?view=azure-dotnet)

## <a name="scenarios"></a>Scénáře

Azure Key Vaults jsou sledované prostředky spravované Správcem prostředků Azure. Azure Resource Manager také určuje dobře definované chování pro odstranění, které vyžaduje, aby úspěšná operace DELETE musí mít za následek, že prostředek již není přístupný. Funkce obnovitelného odstranění řeší obnovení odstraněného objektu bez ohledu na to, zda bylo odstranění náhodné nebo úmyslné.

1. V typickém scénáři může mít uživatel neúmyslně odstranit trezor klíčů nebo objekt trezoru klíčů; Pokud by tento trezor klíčů nebo objekt trezoru klíčů měly být obnovitelné po předem stanovenou dobu, může uživatel odstranění vrátit zpět a obnovit svá data.

2. V jiném scénáři se neautorizovaný uživatel může pokusit odstranit trezor klíčů nebo objekt trezoru klíčů, například klíč uvnitř trezoru, způsobit přerušení podnikání. Oddělení odstranění trezoru klíčů nebo objektu trezoru klíčů od skutečného odstranění podkladových dat lze použít jako bezpečnostní opatření například omezením oprávnění k odstranění dat na jinou důvěryhodnou roli. Tento přístup účinně vyžaduje kvorum pro operaci, která by jinak mohla vést k okamžité ztrátě dat.

### <a name="soft-delete-behavior"></a>Chování při slabém odstranění

Pokud je povoleno obnovitelné odstranění, prostředky označené jako odstraněné prostředky jsou zachovány po zadanou dobu (ve výchozím nastavení 90 dní). Služba dále poskytuje mechanismus pro obnovení odstraněného objektu, v podstatě vrácení odstranění.

Při vytváření nového trezoru klíčů je ve výchozím nastavení zapnuto obnovitelné odstranění. Trezor klíčů můžete vytvořit bez obnovitelného odstranění prostřednictvím [azure cli](key-vault-soft-delete-cli.md) nebo [Azure Powershellu](key-vault-soft-delete-powershell.md). Po povolení obnovitelného odstranění v trezoru klíčů nelze jej zakázat.

Výchozí doba uchovávání je 90 dní, ale během vytváření trezoru klíčů je možné nastavit interval zásad uchovávání informací na hodnotu od 7 do 90 dnů prostřednictvím portálu Azure. Zásady uchovávání ochrany proti vymazání používá stejný interval. Po nastavení nelze změnit interval zásad uchovávání informací.

Název trezoru klíčů, který byl odstraněn, nelze znovu použít, dokud nepomine doba uchování.

### <a name="purge-protection"></a>Ochrana proti pročištění 

Ochrana proti vymazání je volitelné chování trezoru klíčů a **ve výchozím nastavení není povolena**. To může být zapnuto přes [CLI](key-vault-soft-delete-cli.md#enabling-purge-protection) nebo [Powershell](key-vault-soft-delete-powershell.md#enabling-purge-protection).

Pokud je zapnutá ochrana proti vymazání, nelze trezor nebo objekt v odstraněném stavu vymazat, dokud nepomine doba uchování. Úložiště s obnovitelnými odstraněními a objekty lze stále obnovit a zajistit tak, že budou dodrženy zásady uchovávání informací. 

Výchozí doba uchovávání je 90 dní, ale je možné nastavit interval zásad uchovávání informací na hodnotu od 7 do 90 dnů prostřednictvím portálu Azure. Jakmile je nastaven interval zásad uchovávání informací a uložen, nelze jej pro tento trezor změnit. 

### <a name="permitted-purge"></a>Povolené pročištění

Trvalé odstranění, vymazání, trezor klíčů je možné prostřednictvím operace POST na prostředku proxy a vyžaduje zvláštní oprávnění. Obecně platí, že pouze vlastník předplatného bude moci vyčistit trezor klíčů. Operace POST spustí okamžité a neopravitelné odstranění tohoto trezoru. 

Výjimkou jsou:
- Pokud bylo předplatné Azure označeno jako *nepopsatelné*. V takovém případě může provést skutečné odstranění pouze služba a provede tak jako naplánovaný proces. 
- Pokud je v samotném trezoru povolen příznak --enable-purge-protection. V takovém případě bude trezor klíčů čekat 90 dní od doby, kdy byl původní tajný objekt označen k odstranění, aby byl objekt trvale odstraněn.

### <a name="key-vault-recovery"></a>Obnovení trezoru klíčů

Po odstranění trezoru klíčů služba vytvoří prostředek proxy v rámci předplatného a přidá dostatečná metadata pro obnovení. Prostředek proxy je uložený objekt, který je k dispozici ve stejném umístění jako odstraněný trezor klíčů. 

### <a name="key-vault-object-recovery"></a>Obnovení objektu trezoru klíčů

Po odstranění objektu trezoru klíčů, jako je například klíč, služba umístí objekt do odstraněného stavu, takže je nepřístupný pro všechny operace načítání. V tomto stavu může být objekt trezoru klíčů uveden, obnoven nebo násilně/trvale odstraněn. 

Současně trezor klíčů naplánuje odstranění podkladových dat odpovídajících odstraněnému trezoru klíčů nebo objektu trezoru klíčů pro spuštění po předem určeném intervalu uchování. Záznam DNS odpovídající úschovně je také zachován po dobu intervalu uchování.

### <a name="soft-delete-retention-period"></a>Doba uchovávání s měkkým odstraněním

Obnovitelné odstraněné zdroje jsou uchovávány po stanovenou dobu, 90 dní. Během intervalu uchovávání měkkých smazání platí následující:

- Můžete uvést všechny trezory klíčů a objekty trezoru klíčů ve stavu obnovitelného odstranění pro vaše předplatné, stejně jako přístup k odstranění a obnovení informace o nich.
    - Odstraněné trezory mohou vypsat pouze uživatelé se zvláštními oprávněními. Doporučujeme, aby naši uživatelé vytvořili vlastní roli s těmito zvláštními oprávněními pro zpracování odstraněných trezorů.
- Trezor klíčů se stejným názvem nelze vytvořit ve stejném umístění; Odpovídajícím způsobem nelze v daném trezoru vytvořit objekt trezoru klíčů, pokud tento trezor klíčů obsahuje objekt se stejným názvem a který je v odstraněném stavu 
- Pouze konkrétně privilegovaný uživatel může obnovit trezor klíčů nebo objekt trezoru klíčů vydáním příkazu obnovit na odpovídající prostředek proxy.
    - Uživatel, člen vlastní role, který má oprávnění k vytvoření trezoru klíčů pod skupinou prostředků, může úložiště obnovit.
- Pouze konkrétně privilegovaný uživatel může násilně odstranit trezor klíčů nebo objekt trezoru klíčů vydáním příkazu delete na odpovídající prostředek proxy.

Pokud není obnoven trezor klíčů nebo objekt trezoru klíčů, na konci intervalu uchování provede služba vymazání úložiště s klíčem nebo objektu trezoru klíčů a jeho obsahu. Odstranění prostředků nemusí být přeplánováno.

### <a name="billing-implications"></a>Důsledky fakturace

Obecně platí, že pokud je objekt (trezor klíčů nebo klíč nebo tajný klíč) v odstraněném stavu, jsou možné pouze dvě operace: "vyčistit" a "obnovit". Všechny ostatní operace se nezdaří. Proto i v případě, že objekt existuje, nelze provádět žádné operace, a proto nedojde k žádnému použití, takže žádná faktura. Existují však následující výjimky:

- Akce "očištění" a obnovení se započítávají do běžných operací trezoru klíčů a budou účtovány.
- Pokud je objekt klíč HSM, poplatek za klíč chráněný pomocí hsm za verzi klíče za měsíc se účtuje, pokud byla v posledních 30 dnech použita verze klíče. Za to, protože objekt je v odstraněném stavu žádné operace lze provést proti němu, takže žádný poplatek bude platit.

## <a name="next-steps"></a>Další kroky

Následující dva návody nabízejí primární scénáře použití pro použití obnovitelného odstranění.

- [Jak používat obnovitelné odstranění Key Vaultu s využitím PowerShellu](key-vault-soft-delete-powershell.md) 
- [Jak používat obnovitelné odstranění Key Vaultu s využitím CLI](key-vault-soft-delete-cli.md)

