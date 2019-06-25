---
title: Obnovitelné odstranění služby Azure Key Vault | Dokumentace Microsoftu
ms.service: key-vault
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/19/2019
ms.openlocfilehash: 330337620f1732b9ccecfb2c95a0b4495476f97b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64720523"
---
# <a name="azure-key-vault-soft-delete-overview"></a>Přehled obnovitelného odstranění služby Azure Key Vault

Funkce obnovitelného odstranění služby Key Vault umožňuje obnovení odstraněných trezorů a objektů trezoru, říká obnovitelné odstranění. Konkrétně jsme následujících situacích:

- Podpora pro obnovitelné odstranění trezoru klíčů.
- Podpora pro obnovitelné odstranění trezoru klíčů objektů (např.) klíče, tajné kódy, certifikáty)

## <a name="supporting-interfaces"></a>Podpora rozhraní

Funkce obnovitelného odstranění je zpočátku k dispozici prostřednictvím [REST](/rest/api/keyvault/), [rozhraní příkazového řádku](key-vault-soft-delete-cli.md), [PowerShell](key-vault-soft-delete-powershell.md) a [.NET /C# ](/dotnet/api/microsoft.azure.keyvault?view=azure-dotnet) rozhraní.

## <a name="scenarios"></a>Scénáře

Trezory klíčů Azure jsou sledované prostředky spravovat pomocí Azure Resource Manageru. Azure Resource Manageru také určuje jasně definovaných chování pro odstranění, který vyžaduje, že tento prostředek není přístupný už musí být úspěšná operace odstranění. Funkce obnovitelného odstranění řeší obnovení odstraněného objektu, zda byl odstraněn náhodného nebo záměrného.

1. Typický scénář uživatel neúmyslně odstraněna služby key vault nebo objektu služby key vault. Pokud, který klíč trezoru, nebo klíč trezoru objektu chtěli nebude použitelná pro obnovení pro předem určenou dobu, může odstranění vrátit zpět a obnovit svá data uživatele.

2. V případě různých neautorizovaný uživatel může pokus o odstranění služby key vault nebo objekt trezoru klíčů, například klíč v trezoru, způsobit narušení. Odstranění trezoru klíčů nebo objektu služby key vault oddělení od skutečné odstranění podkladová data je možné jako bezpečnostní opatření, omezení oprávnění k odstranění dat na jiný, například důvěryhodnou roli. Tento přístup efektivní vyžaduje kvora pro operace, která může být jinak dojít ke ztrátě okamžité.

### <a name="soft-delete-behavior"></a>Chování obnovitelného odstranění

Operace odstranění objektů služby key vault nebo služby key vault s touto funkcí je obnovitelné odstranění, účinně drží prostředky pro danou uchovávají (90 dnů), zároveň dává vzhled odstranění objektu. Další služby poskytuje mechanismus pro obnovení odstraněného objektu v podstatě vrácení odstranění. 

Volitelné chování služby Key Vault se obnovitelného odstranění a je **není povolená ve výchozím nastavení** v této verzi. Je možné zapnout prostřednictvím [rozhraní příkazového řádku](key-vault-soft-delete-cli.md) nebo [Powershellu](key-vault-soft-delete-powershell.md).

### <a name="purge-protection"></a>Vyprázdnit ochrany 

Při mazání ochrany je na trezor nebo objektu ve stavu odstraněno nelze vymazat, až do uplynutí doby uchovávání těchto 90 dnů. Tyto trezory a objekty je stále možné obnovit, zákazníky je ujištěním postupovali podle zásady uchovávání informací. 

Vyprázdnit ochrany je volitelné chování služby Key Vault a **není povolená ve výchozím nastavení**. Je možné zapnout prostřednictvím [rozhraní příkazového řádku](key-vault-soft-delete-cli.md#enabling-purge-protection) nebo [Powershellu](key-vault-soft-delete-powershell.md#enabling-purge-protection).

### <a name="permitted-purge"></a>Povolené vyprázdnění

Trvale odstranit, odstranění, trezor klíčů je možné prostřednictvím operace POST na prostředku proxy serveru a vyžaduje speciální oprávnění. Obecně platí pouze vlastník předplatného budete moct odstranit trezor klíčů. Operace POST aktivuje okamžitý a nezotavitelnou odstranění tohoto trezoru. 

Výjimky jsou:
- Pokud předplatné Azure bylo označeno jako *neodstranitelný*. V tomto případě pouze služba potom může provést skutečné odstranění a provádí jako proces naplánované. 
- Když--enable--ochrany příznak je povolená v samotném trezoru. Key Vault v tomto případě bude čekat 90 dní od při původní objekt tajného kódu byla označená k odstranění se trvale odstranit objekt.

### <a name="key-vault-recovery"></a>Obnovení služby Key vault

Při odstranění trezoru klíčů, vytvoří službu proxy prostředků v rámci předplatného, přidání dostatečná metadata pro obnovení. Prostředek serveru proxy je uložený objekt k dispozici ve stejném umístění jako odstraněný trezor klíčů. 

### <a name="key-vault-object-recovery"></a>Obnovení objektu služby Key vault

Při odstraňování objektů trezoru klíčů, jako jsou klíče, služba se umístit objekt ve stavu odstraněno, díky tomu je nepřístupný pro jakékoli operace načtení. V tomto stavu objektů trezoru klíčů pouze výpis je možný, obnovené nebo vynuceně/odstranil natrvalo. 

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

### <a name="billing-implications"></a>Vliv na fakturaci

Obecně platí, pokud objekt (trezor klíčů nebo klíč nebo tajný klíč) je ve stavu odstraněno, existují jenom dvě operace možných: "Vymazat" a "obnovit". Všechny ostatní operace se nezdaří. Proto i v případě, že objekt existuje, mohou být prováděny žádné operace, a proto dojde bez využití, tak žádné faktury. Jsou ale následující výjimky:

- akce "Vymazat" a "obnovit" se započítávají do operace normální služby key vault a budou se fakturovat.
- Pokud klíč HSM je objekt, "Klíč chráněný HSM" sazba za verzi klíče za měsíčních poplatků platit, pokud byla použita verze klíče v posledních 30 dní. Po tomto protože objekt je ve stavu odstraněno, které lze provést žádné operace proti, tak žádné zpoplatněná.

## <a name="next-steps"></a>Další postup

Následující dva postupy nabízejí scénáře primárního použití pro použití obnovitelného odstranění.

- [Jak používat obnovitelné odstranění Key Vaultu s využitím PowerShellu](key-vault-soft-delete-powershell.md) 
- [Jak používat obnovitelné odstranění Key Vaultu s využitím CLI](key-vault-soft-delete-cli.md)

