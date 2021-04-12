---
title: Odhlásit z Azure Active Directory ověřitelných přihlašovacích údajů (Preview)
description: Zjistěte, jak se odhlásit z verze Preview ověřitelných přihlašovacích údajů.
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: d6e72b6d6f566fcf3f52e1c48ab6824c0e9a968e
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222788"
---
# <a name="opt-out-of-the-verifiable-credentials-preview"></a>Odhlásit z ověřitelných přihlašovacích údajů (Preview)

V tomto článku:

- Důvod, proč možná budete muset odhlásit.
- Požadované kroky.
- Co se stane s Vašimi daty?
- Vliv na existující ověřitelná pověření.

> [!IMPORTANT]
> Azure Active Directory ověřitelných přihlašovacích údajů je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

- Dokončete ověřitelná pověření pro ověření přihlašovacích údajů.

## <a name="potential-reasons-for-opting-out"></a>Potenciální důvody pro vypnutí

V současné době není možné provádět úpravy informací o doméně. V důsledku toho, pokud uděláte chybu nebo se rozhodnete, že chcete provést změnu, není k dispozici žádná jiná možnost kromě oddálení a opětovného spuštění.

## <a name="the-steps-required"></a>Požadované kroky

1. Z Azure Portal vyhledejte ověřitelné přihlašovací údaje.
2. V nabídce na levé straně vyberte **Nastavení** .
3. V části **obnovte svou organizaci**, vyberte **Odstranit všechny přihlašovací údaje a odhlaste se od verze Preview**.

   ![nastavení resetování org](media/how-to-opt-out/settings-reset.png)

4. Přečtěte si zprávu upozornění a pokračujte výběrem možnosti **Odstranit a odhlásit**.

   ![nastavení DELETE a odsouhlasení](media/how-to-opt-out/delete-and-opt-out.png)

Nyní jste se vyhlásili z verze Preview ověřitelných přihlašovacích údajů. Pokračujte ve čtení a zjistěte, co se děje pod digestoří.

## <a name="what-happens-to-your-data"></a>Co se stane s Vašimi daty?

Až se rozhodnete, že zaškrtnete službu Azure Active Directory ověřitelné přihlašovací údaje, provedou se tyto akce:

- Klíče v Key Vault budou [obnovitelné](../../key-vault/general/soft-delete-overview.md).
- Objekt vystavitele se odstraní z naší databáze.
- Identifikátorem tenanta se odstraní z naší databáze. 
- Všechny objekty smluv budou z naší databáze odstraněny.

Jakmile dojde k výslovným odhlášení, nebudete moct obnovit provedené nebo provádět žádné operace. Tento krok je jednosměrnou operací a musíte se znovu přihlásit, což vede k vytvoření nového.  

## <a name="effect-on-existing-verifiable-credentials"></a>Vliv na existující ověřitelná pověření

Všechny ověřené přihlašovací údaje už jsou vydané, i nadále existují. Nebudou kryptograficky vyhodnoceny jako v případě, že by vaše aplikace zůstaly přeložitelnými.
Pokud však předávající strany volají stavové rozhraní API, vždy se jim vrátí zpráva o selhání.

## <a name="next-steps"></a>Další kroky

- Nastavení ověřitelných přihlašovacích údajů v [Tenantovi Azure](get-started-verifiable-credentials.md)