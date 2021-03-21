---
title: Přidružení partnerského čísla ASN k předplatnému Azure pomocí PowerShellu
titleSuffix: Azure
description: Přidružení partnerského čísla ASN k předplatnému Azure pomocí PowerShellu
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3b463293899dc2586404d68145943caff3105e89
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97590184"
---
# <a name="associate-peer-asn-to-azure-subscription-using-powershell"></a>Přidružení partnerského čísla ASN k předplatnému Azure pomocí PowerShellu

Před odesláním žádosti o vytvoření partnerského vztahu byste nejdřív měli přidružit své ASN k předplatnému Azure pomocí následujících kroků.

Pokud budete chtít, můžete tuto příručku dokončit pomocí [portálu](howto-subscription-association-portal.md).

### <a name="working-with-azure-powershell"></a>Práce s Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>Vytvoření PeerASN k přidružení vašeho ASN k předplatnému Azure

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Přihlaste se ke svému účtu Azure a vyberte své předplatné.
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="register-for-peering-resource-provider"></a>Registrace pro poskytovatele prostředků partnerského vztahu
Zaregistrujte se do poskytovatele prostředků partnerského vztahu ve vašem předplatném Pomocí následujícího příkazu. Pokud to neprovedete, prostředky Azure potřebné k nastavení partnerského vztahu nejsou přístupné.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

Stav registrace můžete kontrolovat pomocí níže uvedených příkazů:
```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

> [!IMPORTANT]
> Než budete pokračovat, počkejte, až *RegistrationState* zaregistrujte. Po provedení příkazu může trvat 5 až 30 minut.

### <a name="update-the-peer-information-associated-with-this-subscription"></a>Aktualizovat informace o partnerském vztahu přidruženého k tomuto předplatnému

Níže je příklad, jak aktualizovat informace o partnerovi.

```powershell
New-AzPeerAsn `
    -Name "Contoso_1234" `
    -PeerName "Contoso" `
    -PeerAsn 1234 `
    -Email noc@contoso.com, support@contoso.com `
    -Phone "+1 (555) 555-5555"
```

> [!NOTE]
> – Název odpovídá názvu prostředku a může to být cokoli, co si zvolíte. Nicméně peerName odpovídá názvu vaší společnosti a musí být co nejblíže vašemu profilu PeeringDB. Všimněte si, že hodnota peerName podporuje pouze znaky a-z, a-Z a mezeru.

Předplatné může mít víc čísla ASN. Aktualizujte informace o partnerském vztahu pro každé číslo ASN. Ujistěte se, že "název" je jedinečný pro každé číslo ASN.

U partnerských vztahů se očekává, že budou mít úplný a aktuální profil pro [PeeringDB](https://www.peeringdb.com). Tyto informace používáme během registrace k ověření detailů partnerského vztahu, jako jsou NOC informace, technické kontaktní informace a jejich přítomnost v zařízeních partnerských vztahů atd.

Všimněte si, že místo **{SubscriptionId}** ve výstupu výše se zobrazí aktuální ID předplatného.

## <a name="view-status-of-a-peerasn"></a>Zobrazit stav PeerASN

Ověřte stav ověření ASN pomocí následujícího příkazu:

```powershell
Get-AzPeerAsn
```

Níže je uvedená příklad odpovědi:
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
> Před odesláním žádosti o partnerský vztah počkejte, než ValidationState zapnete "Schváleno". Toto schválení může trvat až 12 hodin.

## <a name="modify-peerasn"></a>Upravit PeerAsn
Kontaktní údaje NOC můžete kdykoli upravit.

Níže je příklad:

```powershell
Set-PeerAsn -Name Contoso_1234 -Email "newemail@test.com" -Phone "1800-000-0000"
```

## <a name="delete-peerasn"></a>Odstranit PeerAsn
Odstranění PeerASN se v tuto chvíli nepodporuje. Pokud potřebujete odstranit PeerASN, obraťte se na [partnerský vztah Microsoftu](mailto:peering@microsoft.com).

## <a name="next-steps"></a>Další kroky

* [Vytvoření nebo úprava peeringu typu Direct](howto-direct-powershell.md)
* [Převod starší verze peeringu typu Direct na prostředek Azure](howto-legacy-direct-powershell.md)
* [Vytvoření nebo úprava partnerského vztahu serveru Exchange](howto-exchange-powershell.md)
* [Převod starší verze peeringu typu Exchange na prostředek Azure](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Další zdroje informací

Další informace najdete v tématu [Nejčastější dotazy týkající se partnerského vztahu Internetu](faqs.md) .
