---
title: 'Řešení potíží s klientem sítě VPN pro ověřování Azure AD: Azure VPN Gateway | Microsoft Docs'
description: Řešení potíží s VPN Gateway P2S klienty ověřování Azure AD
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: e239f283d5065e032e98e87a72a4bfe9fbafbdb1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517277"
---
# <a name="troubleshoot-an-azure-ad-authentication-vpn-client"></a>Řešení potíží s klientem sítě VPN pro ověřování Azure AD

Tento článek vám pomůže při řešení potíží s klientem VPN pro připojení k virtuální síti pomocí sítě VPN typu Point-to-site a ověřování Azure Active Directory.

## <a name="status"></a>Zobrazit protokol stavu

Zobrazení chybových zpráv v protokolu stavu.

![Protokoly](./media/troubleshoot-ad-vpn-client/1.png)

1. Kliknutím na ikonu se šipkami v pravém dolním rohu okna klienta zobrazíte **protokoly stavu**.
2. V protokolech vyhledejte chyby, které mohou problém indikovat.
3. Chybové zprávy se zobrazují červeně.

## <a name="clear"></a>Vymazat přihlašovací údaje

Vymažte přihlašovací údaje.

![přihlášení](./media/troubleshoot-ad-vpn-client/2.png)

1. Vyberte... vedle profilu, který chcete řešit. Vyberte **Konfigurovat-> vymazat uložený účet**.
2. Vyberte **Save** (Uložit).
3. Pokuste se připojit.
4. Pokud se připojení stále nedaří, pokračujte k další části.

## <a name="diagnostics"></a>Spustit diagnostiku

Spusťte diagnostiku na klientovi VPN.

![Diagnostika](./media/troubleshoot-ad-vpn-client/3.png)

1. Klikněte na **...** vedle profilu, na kterém chcete spustit diagnostiku. Vyberte **Diagnostika-> spustit diagnostiku**.
2. Klient spustí sérii testů a zobrazí výsledek testu.

   * Přístup k Internetu – zkontroluje, jestli má klient připojení k Internetu.
   * Pověření klienta – zkontrolujte, zda je koncový bod ověřování Azure Active Directory dosažitelný.
   * Přeložitelný Server – kontaktuje server DNS, aby přeložil IP adresu nakonfigurovaného serveru VPN.
   * Dostupný server – kontroluje, zda server VPN neodpovídá nebo není.
3. Pokud některý z testů selže, požádejte správce sítě, aby problém vyřešil.
4. V další části se dozvíte, jak shromažďovat protokoly v případě potřeby.

## <a name="logfiles"></a>Shromáždit soubory protokolu klienta

Shromáždění souborů protokolu klienta VPN. Soubory protokolů lze odeslat pro podporu nebo správce pomocí metody, kterou si zvolíte. Například e-mail.

1. Klikněte na "..." vedle profilu, na kterém chcete spustit diagnostiku. Vyberte **Diagnostika-> Zobrazit adresář protokolů**.

   ![Zobrazit protokoly](./media/troubleshoot-ad-vpn-client/4.png)
2. Spustí se Průzkumník Windows do složky, která obsahuje soubory protokolu.

   ![Zobrazit soubor](./media/troubleshoot-ad-vpn-client/5.png)

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [Vytvoření klienta Azure Active Directory pro P2S otevřená připojení VPN, která používají ověřování Azure AD](openvpn-azure-ad-tenant.md).