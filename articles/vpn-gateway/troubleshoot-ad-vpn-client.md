---
title: 'VPN Gateway: řešení potíží s klientem VPN – ověřování Azure AD'
description: Řešení potíží s VPN Gateway P2S klienty ověřování Azure AD
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 56a8514fc2531ba0b18925427814e5bfef7d64bf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "84988098"
---
# <a name="troubleshoot-an-azure-ad-authentication-vpn-client"></a>Řešení potíží s klientem sítě VPN pro ověřování Azure AD

Tento článek vám pomůže při řešení potíží s klientem VPN pro připojení k virtuální síti pomocí sítě VPN typu Point-to-site a ověřování Azure Active Directory.

## <a name="view-status-log"></a><a name="status"></a>Zobrazit protokol stavu

Zobrazení chybových zpráv v protokolu stavu.

![Protokoly](./media/troubleshoot-ad-vpn-client/1.png)

1. Kliknutím na ikonu se šipkami v pravém dolním rohu okna klienta zobrazíte **protokoly stavu**.
2. V protokolech vyhledejte chyby, které mohou problém indikovat.
3. Chybové zprávy se zobrazují červeně.

## <a name="clear-sign-in-information"></a><a name="clear"></a>Vymazat přihlašovací údaje

Vymažte přihlašovací údaje.

![přihlášení](./media/troubleshoot-ad-vpn-client/2.png)

1. Vyberte... vedle profilu, který chcete řešit. Vyberte **Konfigurovat-> vymazat uložený účet**.
2. Vyberte **Uložit**.
3. Pokuste se připojit.
4. Pokud se připojení stále nedaří, pokračujte k další části.

## <a name="run-diagnostics"></a><a name="diagnostics"></a>Spuštění diagnostiky

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

## <a name="collect-client-log-files"></a><a name="logfiles"></a>Shromáždit soubory protokolu klienta

Shromáždění souborů protokolu klienta VPN. Soubory protokolů lze odeslat pro podporu nebo správce pomocí metody, kterou si zvolíte. Například e-mail.

1. Klikněte na "..." vedle profilu, na kterém chcete spustit diagnostiku. Vyberte **Diagnostika-> Zobrazit adresář protokolů**.

   ![Zobrazit protokoly](./media/troubleshoot-ad-vpn-client/4.png)
2. Spustí se Průzkumník Windows do složky, která obsahuje soubory protokolu.

   ![Zobrazit soubor](./media/troubleshoot-ad-vpn-client/5.png)

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [Vytvoření klienta Azure Active Directory pro P2S otevřená připojení VPN, která používají ověřování Azure AD](openvpn-azure-ad-tenant.md).