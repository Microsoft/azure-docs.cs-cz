---
title: Azure Key Vault Soft-DELETE | Microsoft Docs
description: Obnovitelné odstranění v Azure Key Vault umožňuje obnovit odstraněné trezory klíčů a objekty trezoru klíčů, jako jsou klíče, tajné klíče a certifikáty.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/30/2020
ms.openlocfilehash: c8ae10fa059bb9cfd32b95f9bc6d21f30ad9f880
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91744198"
---
# <a name="azure-key-vault-soft-delete-overview"></a>Přehled obnovitelného odstranění ve službě Azure Key Vault

> [!IMPORTANT]
> V trezorech klíčů musíte okamžitě povolit obnovitelné odstranění. Možnost Odhlásit se od obnovitelného odstranění bude zastaralá na konci roku a ochrana s antiodstraněním se automaticky zapne pro všechny trezory klíčů.  Zobrazit [Úplné podrobnosti](soft-delete-change.md)

Funkce obnovitelného odstranění Key Vault umožňuje obnovení odstraněných trezorů a odstraněných objektů trezoru klíčů (například klíče, tajné klíče a certifikáty), označované jako obnovitelné odstranění. Konkrétně řešíme následující scénáře: Tato ochrana nabízí následující ochrany:

- Po odstranění tajného klíče, klíče, certifikátu nebo trezoru klíčů zůstane tato možnost obnovitelné po konfigurovatelnou dobu 7 až 90 kalendářních dnů. Pokud není zadána žádná konfigurace, výchozí doba obnovení bude nastavena na 90 dní. Tato operace uživatelům poskytne dostatek času na oznámení nechtěného odstranění tajného klíče a na reakci.
- K trvalému odstranění tajného kódu je nutné provést dvě operace. Nejdřív musí uživatel objekt odstranit, takže ho vloží do stavu bez odstranění. Za druhé musí uživatel vyprázdnit objekt ve stavu obnovitelného odstranění. Operace vyprázdnění vyžaduje další oprávnění zásad přístupu. Tato dodatečná ochrana snižuje riziko, že uživatel omylem nebo škodlivým způsobem odstranil tajný klíč nebo Trezor klíčů.  
- Pokud chcete vyprázdnit tajný klíč ve stavu obnovitelného odstranění, musí být instančnímu objektu udělené další oprávnění "Vyprázdnit" zásady přístupu. Oprávnění k vyprázdnit zásady přístupu není ve výchozím nastavení uděleno žádnému instančnímu objektu, včetně trezoru klíčů a vlastníků předplatného. musí být záměrně nastaveno. Když budete vyžadovat zásadu přístupu se zvýšeným oprávněním k vymazání tajného kódu, sníží se pravděpodobnost nechtěného odstranění tajného klíče.

## <a name="supporting-interfaces"></a>Podpůrná rozhraní

Funkce obnovitelného odstranění je k dispozici prostřednictvím [REST API](/rest/api/keyvault/), rozhraní [Azure CLI](soft-delete-cli.md), [Azure PowerShell](soft-delete-powershell.md)a [.NET/C#](/dotnet/api/microsoft.azure.keyvault?view=azure-dotnet) a také [šablony ARM](/azure/templates/microsoft.keyvault/2019-09-01/vaults).

## <a name="scenarios"></a>Scénáře

Trezory klíčů Azure jsou sledované prostředky spravované pomocí Azure Resource Manager. Azure Resource Manager také určuje dobře definované chování pro odstranění, které vyžaduje, aby úspěšně prováděná operace odstranění měla za následek to, že prostředek již nebude přístupný. Funkce obnovitelného odstranění řeší obnovení odstraněného objektu, bez ohledu na to, zda bylo odstranění náhodné nebo úmyslné.

1. V typickém scénáři mohl uživatel omylem odstranit Trezor klíčů nebo objekt trezoru klíčů. Pokud by tento trezor klíčů nebo objekt trezoru klíčů měl být pro předem stanovenou dobu obnovitelný, uživatel může zrušit odstranění a obnovit jeho data.

2. V jiném scénáři se neautorizovaný uživatel může pokusit odstranit Trezor klíčů nebo objekt trezoru klíčů, jako je například klíč v trezoru, a způsobit tak narušení podniku. Oddělení odstranění trezoru klíčů nebo objektu trezoru klíčů ze skutečného odstranění podkladových dat lze použít jako bezpečnostní opatření, například při omezení oprávnění k odstranění dat na jinou důvěryhodnou roli. Tento přístup efektivně vyžaduje kvorum pro operaci, která by jinak způsobila bezprostřední ztrátu dat.

### <a name="soft-delete-behavior"></a>Chování obnovitelného odstranění

Pokud je povolené obnovitelné odstranění, prostředky označené jako odstraněné prostředky se uchovávají po určenou dobu (ve výchozím nastavení jsou to 90 dny). Služba dále poskytuje mechanismus pro obnovování odstraněného objektu, v podstatě zrušení odstranění.

Při vytváření nového trezoru klíčů je ve výchozím nastavení zapnuté obnovitelné odstranění. Trezor klíčů můžete vytvořit bez obnovitelného odstranění prostřednictvím rozhraní příkazového [řádku Azure](soft-delete-cli.md) nebo [Azure PowerShell](soft-delete-powershell.md). Po povolení obnovitelného odstranění u trezoru klíčů ho nejde zakázat.

Výchozí doba uchování je 90 dní, ale během vytváření trezoru klíčů je možné nastavit interval zásad uchovávání informací na hodnotu od 7 do 90 dnů pomocí Azure Portal. Zásady uchovávání informací o vyprázdnění ochrany používají stejný interval. Po nastavení se interval zásad uchovávání dat nedá změnit.

Nemůžete znovu použít název trezoru klíčů, který byl odstraněn, dokud neuplyne doba uchování.

### <a name="purge-protection"></a>Vyprázdnit ochranu

Vyprázdnit ochranu je volitelné Key Vault chování a není **ve výchozím nastavení povolené**. Ochranu vyprázdnění je možné povolit jenom v případě, že je povolené obnovitelné odstranění.  Dá se zapnout přes rozhraní příkazového [řádku](soft-delete-cli.md#enabling-purge-protection) nebo [PowerShellu](soft-delete-powershell.md#enabling-purge-protection).

Pokud je zapnutá ochrana vyprázdnění, trezor nebo objekt ve stavu odstraněno nelze odstranit, dokud neuplyne doba uchování. Obnovitelné odstraněné trezory a objekty je stále možné obnovit, aby se zajistilo, že budou dodrženy zásady uchovávání informací.

Výchozí doba uchování je 90 dní, ale je možné nastavit interval zásad uchovávání informací na hodnotu od 7 do 90 dnů pomocí Azure Portal. Jakmile je interval zásad uchovávání a uložený, nedá se pro tento trezor změnit.

### <a name="permitted-purge"></a>Povolené vyprázdnění

Trvalé odstranění a vymazání trezoru klíčů je možné prostřednictvím operace POST na prostředku proxy serveru a vyžaduje zvláštní oprávnění. Trezor klíčů bude moci vyprázdnit jenom vlastník předplatného. Operace POST aktivuje okamžité a nezotavitelné odstranění tohoto trezoru. 

Výjimky jsou:
- Když je předplatné Azure označené jako *neodstranitelné*. V takovém případě může provedení samotného odstranění provést pouze služba, a to v rámci plánovaného procesu. 
- Když je tato možnost `--enable-purge-protection flag` povolená v samotném trezoru. V takovém případě bude Key Vault čekat na 90 dní od okamžiku, kdy byl původní tajný objekt označen pro odstranění, aby se objekt trvale odstranil.

Postup najdete v tématu [použití Key Vault obnovitelného odstranění pomocí rozhraní příkazového řádku: vymazání trezoru klíčů](soft-delete-cli.md#purging-a-key-vault) nebo [použití Key Vault obnovitelného odstranění pomocí prostředí PowerShell: vymazání trezoru klíčů](soft-delete-powershell.md#purging-a-key-vault).

### <a name="key-vault-recovery"></a>Obnovení trezoru klíčů

Po odstranění trezoru klíčů vytvoří služba v rámci předplatného prostředek proxy a přidá dostatečné metadata pro obnovení. Prostředek proxy je uložený objekt, který je k dispozici ve stejném umístění jako odstraněný Trezor klíčů. 

### <a name="key-vault-object-recovery"></a>Obnovení objektu trezoru klíčů

Po odstranění objektu trezoru klíčů, jako je třeba klíč, služba umístí objekt do odstraněného stavu, takže nebude přístupný pro žádné operace načítání. V tomto stavu může být objekt trezoru klíčů uveden pouze v seznamu, obnoven nebo vynuceně/trvale odstraněn. Chcete-li zobrazit objekty, použijte příkaz Azure CLI `az keyvault key list-deleted` (jak je popsáno v tématu [jak používat Key Vault obnovitelného odstranění pomocí](soft-delete-cli.md)rozhraní příkazového řádku), nebo `-InRemovedState` parametr Azure PowerShell (jak je popsáno v tématu [Jak používat Key Vault obnovitelného odstranění pomocí prostředí PowerShell](soft-delete-powershell.md#secrets)).  

Ve stejnou dobu Key Vault naplánovat odstranění podkladových dat odpovídajících odstraněnému trezoru klíčů nebo objektu trezoru klíčů, který se spustí po předem určeném intervalu uchování. Záznam DNS odpovídající trezoru se zachovává i po dobu trvání intervalu uchování.

### <a name="soft-delete-retention-period"></a>Doba uchování obnovitelného odstranění

Obnovitelné odstraněné prostředky se uchovávají po nastavené časové období, 90 dnů. Během doby uchování obnovitelného odstranění platí následující:

- Můžete uvést všechny trezory klíčů a objekty trezoru klíčů ve stavu tichého odstranění pro vaše předplatné a také informace o odstranění a obnovení přístupu.
  - Odstraněné trezory můžou vypsat jenom uživatelé se speciálními oprávněními. Doporučujeme, aby naši uživatelé vytvořili vlastní roli s těmito speciálními oprávněními pro zpracování odstraněných trezorů.
- Trezor klíčů se stejným názvem se nedá vytvořit ve stejném umístění. odpovídajícím způsobem nelze v daném trezoru vytvořit objekt trezoru klíčů, pokud tento trezor klíčů obsahuje objekt se stejným názvem, který je v odstraněném stavu.
- Trezor klíčů nebo objekt trezoru klíčů může obnovit pouze konkrétně privilegovaný uživatel vyvoláním příkazu Recovery na odpovídajícím prostředku proxy serveru.
  - Uživatel (člen vlastní role), který má oprávnění k vytvoření trezoru klíčů v rámci skupiny prostředků, může obnovit trezor.
- Pouze speciálně privilegovaný uživatel může nuceně odstranit Trezor klíčů nebo objekt trezoru klíčů vyvoláním příkazu pro odstranění na odpovídajícím prostředku proxy serveru.

Pokud není obnovený Trezor klíčů nebo objekt trezoru klíčů, na konci intervalu uchovávání dat služba vyprázdní odstraněný Trezor klíčů nebo objekt trezoru klíčů a jeho obsah. Odstranění prostředku se nedá přeplánovat.

### <a name="billing-implications"></a>Důsledky fakturace

Obecně platí, že pokud je objekt (Trezor klíčů nebo klíč nebo tajný klíč) v odstraněném stavu, jsou možné pouze dvě operace: ' vyprázdnění ' a ' Recover '. Všechny ostatní operace selžou. Proto i když objekt existuje, nemůžete provádět žádné operace, a proto nedojde k žádnému využití, takže nebude účtována žádná faktura. Existují však následující výjimky:

- akce vyprázdnění a obnovení se počítají do normálních operací trezoru klíčů a budou se fakturovat.
- Pokud je objektem klíč HSM, bude se účtovat poplatek za klíč chráněný modulem HSM na verzi klíče za měsíc, pokud se v posledních 30 dnech použije klíčová verze. Vzhledem k tomu, že je objekt v odstraněném stavu, nelze s ním provádět žádné operace, takže se žádné poplatky nepoužijí.

## <a name="next-steps"></a>Další kroky

Následující dvě příručky nabízejí základní scénáře použití pro použití obnovitelného odstranění.

- [Jak používat obnovitelné odstranění Key Vaultu s využitím PowerShellu](soft-delete-powershell.md) 
- [Jak používat obnovitelné odstranění Key Vaultu s využitím CLI](soft-delete-cli.md)