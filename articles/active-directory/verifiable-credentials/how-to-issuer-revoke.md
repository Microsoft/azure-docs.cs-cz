---
title: Postup odvolání ověřitelných přihlašovacích údajů jako vystavitele Azure Active Directory ověřitelných přihlašovacích údajů
description: Informace o odvolání ověřitelných přihlašovacích údajů, které jste vystavili
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 50f270dde59860e7c9dd2ac1b35039d5855859e5
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222839"
---
# <a name="revoke-a-previously-issued-verifiable-credential-preview"></a>Odvolání dříve vystaveného ověřitelných přihlašovacích údajů (Preview)

V rámci procesu práce s ověřitelnými přihlašovacími údaji (VCs) nemusíte zadávat přihlašovací údaje, ale někdy je také musíte odvolat. V tomto článku se podíváme na část vlastnosti **status** specifikace VC a podíváme se na proces odvolání, proč můžeme chtít odvolat přihlašovací údaje a některá data a dopady na ochranu osobních údajů.

> [!IMPORTANT]
> Azure Active Directory ověřitelných přihlašovacích údajů je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="status-property-in-verifiable-credentials-specification"></a>Vlastnost stavu ve specifikaci ověřitelných přihlašovacích údajů

Než budeme porozumět důsledkům odvolání ověřitelných přihlašovacích údajů, může vám to porozumět tomu, co se jedná o **kontrolu stavu** a jak funguje v dnešní době.

[Specifikace přihlašovacích údajů ověřitelných pověření W3C](https://www.w3.org/TR/vc-data-model/) odkazuje na vlastnost **status** v oddílu [4,9:](https://www.w3.org/TR/vc-data-model/#status)

"Tato specifikace definuje následující vlastnost **credentialStatus** pro zjišťování informací o aktuálním stavu ověřitelných přihlašovacích údajů, například o tom, jestli je pozastavená nebo odvolaná."

Specifikace W3C však nedefinuje formát způsobu implementace **kontroly stavu** .

"Definování datového modelu, formátů a protokolů stavových schémat není v oboru pro tuto specifikaci. Existuje ověřitelný registr rozšíření přihlašovacích údajů [VC-EXTENSION-REGISTRY], který obsahuje dostupná stavová schémata pro implementátory, kteří chtějí implementovat ověřitelnou kontrolu stavu přihlašovacích údajů.

>[!NOTE]
>V současné době je implementace kontroly stavu společnosti Microsoft proprietární, ale aktivně pracujeme s komunitou, abychom mohli standardně zarovnávat.

## <a name="how-does-the-status-property-work"></a>Jak funguje vlastnost **stav** ?

V každé ověřené přihlašovací údaje vydané společností Microsoft existuje atribut s názvem credentialStatus. Vyplní se stavovým rozhraním API, které spravuje Microsoft vaším jménem. Tady je příklad toho, co vypadá jako.

```json
    "credentialStatus": {
      "id": "https://portableidentitycards.azure-api.net/v1.0/7952032d-d1f3-4c65-993f-1112dab7e191/portableIdentities/card/status",
      "type": "PortableIdentityCardServiceCredentialStatus2020"
    }
```

Open Source sada SDK ověřitelných přihlašovacích údajů zpracovává volání rozhraní API stavu a poskytuje potřebná data.

Jakmile se rozhraní API zavolá a poskytne správné informace, rozhraní API vrátí hodnotu true nebo false. Hodnota true znamená, že ověřovací pověření jsou stále aktivní u vystavitele a hodnota false signalizuje, že vystavitelem je aktivně odvolala ověřitelná pověření.

## <a name="why-you-may-want-to-revoke-a-vc"></a>Proč možná budete chtít odvolat VC?

Každý zákazník bude mít svůj vlastní jedinečný důvod pro odvolání ověřitelných přihlašovacích údajů, ale tady je několik běžných motivů, které jsme doposud slyšeli. 

- ID studenta: student už není aktivním studentem na univerzitě.
- ID zaměstnance: zaměstnanec už není aktivním zaměstnancem.
- Licence pro ovladače stavu: ovladač již v tomto stavu žije.

## <a name="how-to-set-up-a-verifiable-credential-with-the-ability-to-revoke"></a>Jak nastavit ověřitelné přihlašovací údaje s možností odvolat

Všechna ověřitelná data přihlašovacích údajů se ve výchozím nastavení neukládají s Microsoftem. Proto nemáme žádná data, která by odkazovala na odvolání konkrétního ověřitelnýho ID přihlašovacích údajů. Vystavitel musí zadat konkrétní pole z atributu ověřitelných přihlašovacích údajů pro Microsoft k indexování a následně příměsi a hodnoty hash.

>[!NOTE]
>Hashing je jednosměrná kryptografická operace, která umožňuje vstup, nazývaný a ```preimage``` , a vytváří výstup s názvem hash s pevnou délkou. V tuto chvíli není možné vyhodnotit operaci hash.

Můžete říct Microsoftu, který atribut ověřitelných přihlašovacích údajů byste chtěli indexovat. Denásobení indexování znamená, že indexované hodnoty lze použít k vyhledávání ověřitelných přihlašovacích údajů pro VC, který chcete odvolat.

**Příklad:** Alice je Woodgrove zaměstnanec. Alice vlevo Woodgrove pracovat ve společnosti Contoso. Jana (správce IT) pro Woodgrove vyhledá v e-mailech na ověřitelných přihlašovacích dotazech hledaný e-mail od Alice. V tomto příkladu Jana, indexované pole e-mail ověřeného pověření zaměstnance Woodgrove. 

Níže najdete příklad toho, jak se upraví soubor pravidel, aby obsahoval index.

```json
{
  "attestations": {
    "idTokens": [
      { 
        "mapping": {
          "Name": { "claim": "name" },
          "email": { "claim": "email", "indexed": true}
        },
        "configuration": "https://login.microsoftonline.com/tenant-id-here7/v2.0/.well-known/openid-configuration",
        "client_id": "c0d6b785-7a08-494e-8f63-c30744c3be2f",
        "redirect_uri": "vcclient://openid"
      }
    ]
  },
  "validityInterval": 25920000,
  "vc": {
    "type": ["WoodgroveEmployee"]
  }
}
```

>[!NOTE]
>Ze souboru pravidel lze indexovat pouze jeden atribut.  

## <a name="how-do-i-revoke-a-verifiable-credential"></a>Návody odvolání ověřitelných přihlašovacích údajů

Jakmile pro uživatele vydáte deklaraci identity a ověřitelné přihlašovací údaje, je čas zjistit, jak můžete v okně VC odvolat ověřitelné přihlašovací údaje.

1. V Azure Active Directory přejděte do okna ověřitelné přihlašovací údaje.
1. Vyberte ověřitelné přihlašovací údaje, u kterých jste už dříve nastavili deklaraci identity, a pak uživateli vystavili ověřitelné přihlašovací údaje. =
1. V nabídce na levé straně vyberte **odvolání přihlašovacích údajů** 
    ![ odvolat přihlašovací údaje.](media/how-to-issuer-revoke/settings-revoke.png) 
1. Vyhledejte atribut indexu uživatele, kterého chcete odvolat. 

   ![Vyhledat přihlašovací údaje, které se mají odvolat](media/how-to-issuer-revoke/revoke-search.png)

    >[!NOTE]
    >Vzhledem k tomu, že ukládáme jenom hodnotu hash indexované deklarace identity z ověřitelných přihlašovacích údajů, naplní se do výsledků hledání jenom přesná shoda. Vstup probereme jako vyhledaný správcem IT a používáme stejný algoritmus hash, abyste zjistili, jestli se v naší databázi shoduje hodnota hash.
    
1. Po nalezení shody vyberte možnost **odvolat** napravo od přihlašovacích údajů, které chcete odvolat.

   ![Upozornění, které vám dává informace o tom, že po odvolání má uživatel nadále přihlašovací údaje](media/how-to-issuer-revoke/warning.png) 

1. Po úspěšném odvolání se zobrazí aktualizace stavu a v horní části stránky se zobrazí zelená banner. 
   ![Ověřit tuto doménu v nastaveních](media/how-to-issuer-revoke/revoke-successful.png) 

Když se teď předávající strana zavolá, aby zkontrolovala stav tohoto konkrétního ověřitelných přihlašovacích údajů, vrátí rozhraní API stavu Microsoftu, které funguje jménem tenanta, odpověď false.

## <a name="next-steps"></a>Další kroky

Otestujte funkci sami pomocí testovacího pověření, které se má použít pro tok. Informace o tom, jak nakonfigurovat svého tenanta tak, aby vydával ověřitelné přihlašovací údaje, najdete v [našich kurzech](get-started-verifiable-credentials.md).