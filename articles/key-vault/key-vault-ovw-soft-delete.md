---
ms.assetid: ''
title: Obnovitelné odstranění služby Azure Key Vault | Dokumentace Microsoftu
ms.service: key-vault
author: bryanla
ms.author: bryanla
manager: mbaldwin
ms.date: 09/25/2017
ms.openlocfilehash: ccdefc83642285194635ffe7b561e9e322360533
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2018
ms.locfileid: "42056796"
---
# <a name="azure-key-vault-soft-delete-overview"></a>Přehled obnovitelného odstranění služby Azure Key Vault

Funkce obnovitelného odstranění služby Key Vault umožňuje obnovení odstraněných trezorů a objektů trezoru, říká obnovitelné odstranění. Konkrétně jsme následujících situacích:

- Podpora pro obnovitelné odstranění trezoru klíčů.
- Podpora pro obnovitelné odstranění trezoru klíčů objektů (např.) klíče, tajné kódy, certifikáty)

## <a name="supporting-interfaces"></a>Podpora rozhraní

Funkce obnovitelného odstranění je zpočátku k dispozici prostřednictvím rozhraní REST, .NET / C#, rozhraní prostředí PowerShell a rozhraní příkazového řádku.

Obecné informace najdete v článku odkazy na tyto další podrobnosti najdete [Key Vault Reference](https://docs.microsoft.com/azure/key-vault/).

## <a name="scenarios"></a>Scénáře

Trezory klíčů Azure jsou sledované prostředky spravovat pomocí Azure Resource Manageru. Azure Resource Manageru také určuje jasně definovaných chování pro odstranění, který vyžaduje, že tento prostředek není přístupný už musí být úspěšná operace odstranění. Funkce obnovitelného odstranění řeší obnovení odstraněného objektu, zda byl odstraněn náhodného nebo záměrného.

1. Typický scénář uživatel neúmyslně odstraněna služby key vault nebo objektu služby key vault. Pokud, který klíč trezoru, nebo klíč trezoru objektu chtěli nebude použitelná pro obnovení pro předem určenou dobu, může odstranění vrátit zpět a obnovit svá data uživatele.

2. V případě různých neautorizovaný uživatel může pokus o odstranění služby key vault nebo objekt trezoru klíčů, například klíč v trezoru, způsobit narušení. Odstranění trezoru klíčů nebo objektu služby key vault oddělení od skutečné odstranění podkladová data je možné jako bezpečnostní opatření, omezení oprávnění k odstranění dat na jiný, například důvěryhodnou roli. Tento přístup efektivní vyžaduje kvora pro operace, která může být jinak dojít ke ztrátě okamžité.

### <a name="soft-delete-behavior"></a>Chování obnovitelného odstranění

Operace odstranění objektů služby key vault nebo služby key vault s touto funkcí je obnovitelné odstranění, účinně drží prostředky pro danou dobu uchování o, zároveň dává vzhled odstranění objektu. Další služby poskytuje mechanismus pro obnovení odstraněného objektu v podstatě vrácení odstranění. 

Volitelné chování služby Key Vault se obnovitelného odstranění a je **není povolená ve výchozím nastavení** v této verzi. 

### <a name="key-vault-recovery"></a>Obnovení služby Key vault

Při odstranění trezoru klíčů, vytvoří službu proxy prostředků v rámci předplatného, přidání dostatečná metadata pro obnovení. Prostředek serveru proxy je uložený objekt k dispozici ve stejném umístění jako odstraněný trezor klíčů. 

### <a name="key-vault-object-recovery"></a>Obnovení objektu služby Key vault

Při odstraňování objektů trezoru klíčů, jako jsou klíče, služba se umístit objekt ve stavu odstraněno, díky čemuž nepřístupný pro jakékoli operace načtení. V tomto stavu objektů trezoru klíčů pouze výpis je možný, obnovené nebo vynuceně/odstranil natrvalo. 

Ve stejnou dobu služby Key Vault se naplánuje odstranění podkladová data odpovídající objekt služby key vault pro provádění za dobu uchování předem nebo odstraněný trezor klíčů. DNS záznam odpovídající do trezoru se také uchovávají po dobu trvání interval uchovávání informací.

### <a name="soft-delete-retention-period"></a>Doba uchování obnovitelného odstranění

Obnovitelně odstraněný prostředky se uchovávají nastavte časový úsek, 90 dnů. Během intervalu uchovávání obnovitelného odstranění následujících podmínek:

- Může výpis všech trezorů klíčů a objektů trezoru klíčů ve stavu obnovitelného odstranění pro vaše předplatné, jakož i přístup k odstranění a obnovení informací o nich.
    - Jenom uživatelé se zvláštními oprávněními můžete zobrazit seznam odstraněných trezorů. Doporučujeme, aby naši uživatelé vytvořit vlastní roli s tato zvláštní oprávnění pro zpracování odstranit trezory.
- Nelze vytvořit trezor klíčů se stejným názvem ve stejném umístění; odpovídajícím způsobem trezor klíčů objekt nelze vytvořit v daném trezoru Pokud tento trezor klíčů obsahuje objekt se stejným názvem a který je ve stavu odstraněno 
- Pouze konkrétně privilegovaných uživatelů může obnovit služby key vault nebo objekt služby key vault pomocí příkazu Obnovit na odpovídající prostředek proxy.
    - Uživatel členem vlastní role, který má oprávnění k vytvoření služby key vault ve skupině prostředků můžete obnovit v trezoru.
- Pouze konkrétně privilegovaných uživatelů může vynuceně trezoru klíčů nebo odstranit objekt služby key vault vydání příkazu delete na odpovídající prostředek proxy serveru.

Pokud obnovení služby key vault nebo objekt trezoru klíčů, provádí služba na konci interval uchovávání informací vymazat obnovitelně odstraněný trezor klíčů nebo objektu služby key vault a její obsah. Nelze ho přeplánovat odstranění prostředku.

### <a name="permitted-purge"></a>Povolené vyprázdnění

Trvale odstranit, odstranění, trezor klíčů je možné prostřednictvím operace POST na prostředku proxy serveru a vyžaduje speciální oprávnění. Obecně platí pouze vlastník předplatného budete moct odstranit trezor klíčů. Operace POST aktivuje okamžitý a nezotavitelnou odstranění tohoto trezoru. 

Výjimkou je případ, kdy předplatné Azure bylo označeno jako *neodstranitelný*. V tomto případě pouze služba potom může provést skutečné odstranění a provádí jako proces naplánované. 

### <a name="billing-implications"></a>Vliv na fakturaci

Obecně platí, pokud objekt (trezor klíčů nebo klíč nebo tajný klíč) je ve stavu odstraněno, existují jenom dvě operace možných: "Vymazat" a "obnovit". Všechny ostatní operace se nezdaří. Proto i v případě, že objekt existuje, mohou být prováděny žádné operace, a proto dojde bez využití, tak žádné faktury. Jsou ale následující výjimky:

- akce "Vymazat" a "obnovit" se započítávají do operace normální služby key vault a budou se fakturovat.
- Pokud klíč HSM je objekt, "Klíč chráněný HSM" sazba za verzi klíče za měsíčních poplatků platit, pokud byla použita verze klíče v posledních 30 dní. Po tomto protože objekt je ve stavu odstraněno, které lze provést žádné operace proti, tak žádné zpoplatněná.

## <a name="next-steps"></a>Další postup

Následující dva postupy nabízejí scénáře primárního použití pro použití obnovitelného odstranění.

- [Jak používat obnovitelné odstranění Key Vaultu s využitím PowerShellu](key-vault-soft-delete-powershell.md) 
- [Jak používat obnovitelné odstranění Key Vaultu s využitím CLI](key-vault-soft-delete-cli.md)

