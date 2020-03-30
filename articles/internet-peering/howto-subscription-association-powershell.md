---
title: Přidružení přidružení partnerského partnera ASN k předplatnému Azure pomocí PowerShellu
titleSuffix: Azure
description: Přidružení přidružení partnerského partnera ASN k předplatnému Azure pomocí PowerShellu
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 77cc4732e017d95cbae19578cf26b1111b08fdde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75908990"
---
# <a name="associate-peer-asn-to-azure-subscription-using-powershell"></a>Přidružení přidružení partnerského partnera ASN k předplatnému Azure pomocí PowerShellu

Před odesláním požadavku partnerského vztahu byste měli nejprve přidružit asn s předplatným Azure pomocí následujících kroků.

Pokud chcete, můžete tuto příručku dokončit pomocí [portálu](howto-subscription-association-portal.md).

### <a name="working-with-azure-powershell"></a>Práce s Azure PowerShellem
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>Vytvoření programu PeerASN pro přidružení vašeho asn k předplatnému Azure

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Přihlaste se ke svému účtu Azure a vyberte předplatné.
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="register-for-peering-resource-provider"></a>Zaregistrovat se pro zprostředkovatele prostředků partnerského vztahu
Zaregistrujte se pro zprostředkovatele partnerského vztahu v předplatném pomocí níže uvedeného příkazu. Pokud to neprovedete, prostředky Azure potřebné k nastavení partnerského vztahu nejsou přístupné.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

Stav registrace můžete zkontrolovat pomocí následujících příkazů:
```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

> [!IMPORTANT]
> Počkejte *na RegistrationState* zase "Registrováno" před pokračováním. Po spuštění příkazu může trvat 5 až 30 minut.

### <a name="update-the-peer-information-associated-with-this-subscription"></a>Aktualizace informací partnera přidružených k tomuto předplatnému

Níže je uveden příklad aktualizace informací o partnerské straně.

```powershell
New-AzPeerAsn `
    -Name "Contoso_1234" `
    -PeerName "Contoso" `
    -PeerAsn 1234 `
    -Email noc@contoso.com, support@contoso.com `
    -Phone "+1 (555) 555-5555"
```

> [!NOTE]
> -Název odpovídá názvu prostředku a může být cokoliv, co si vyberete. Však -peerName odpovídá názvu vaší společnosti a musí být co nejblíže profilu PeeringDB. Všimněte si, že hodnota pro -peerName podporuje pouze znaky a-z, A-Z a mezeru.

Předplatné může mít více asns. Aktualizujte informace o partnerském vztahu pro každý asn. Ujistěte se, že "název" je jedinečný pro každý ASN.

Očekává se, že partneři budou mít úplný a aktuální profil v [PeeringDB](https://www.peeringdb.com). Tyto informace používáme při registraci k ověření podrobností partnera, jako jsou informace o NOC, technické kontaktní informace a jejich přítomnost v peeringových zařízeních atd.

Všimněte si, že místo **{subscriptionId}** ve výstupu výše se zobrazí skutečné ID předplatného.

## <a name="view-status-of-a-peerasn"></a>Zobrazit stav peerasn

Zkontrolujte stav ověření ASN pomocí následujícího příkazu:

```powershell
Get-AzPeerAsn
```

Níže je příklad odpovědi:
```powershell
PeerContactInfo : Microsoft.Azure.PowerShell.Cmdlets.Peering.Models.PSContactInfo
PeerName        : Contoso
ValidationState : Approved
PeerAsnProperty : 1234
Name            : Contoso_1234
Id              : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/Contoso_1234
Type            : Microsoft.Peering/peerAsns
```

> [!IMPORTANT]
> Před odesláním požadavku partnerského vztahu počkejte na stav ValidationState, který se změní na "Schváleno". Toto schválení může trvat až 12 hodin.

## <a name="modify-peerasn"></a>Upravit PeerAsn
Kontaktní údaje NOC můžete kdykoli upravit.

Níže je uveden příklad:

```powershell
Set-PeerAsn -Name Contoso_1234 -Email "newemail@test.com" -Phone "1800-000-0000"
```

## <a name="delete-peerasn"></a>Odstranit PeerAsn
Odstranění nepodporovaného souboru PeerASN není aktuálně podporováno. Pokud potřebujete odstranit peerasn, obraťte se na [partnerský vztah společnosti Microsoft](mailto:peering@microsoft.com).

## <a name="next-steps"></a>Další kroky

* [Vytvoření nebo úprava peeringu typu Direct](howto-direct-powershell.md)
* [Převod starší verze peeringu typu Direct na prostředek Azure](howto-legacy-direct-powershell.md)
* [Vytvoření nebo úprava partnerského vztahu Exchange](howto-exchange-powershell.md)
* [Převod starší verze peeringu typu Exchange na prostředek Azure](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Další zdroje

Další informace naleznete v [nejčastějších dotazech k internetovému partnerovi](faqs.md)
