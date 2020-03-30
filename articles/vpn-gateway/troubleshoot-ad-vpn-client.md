---
title: 'Brána VPN: Poradce při potížích s klientem VPN – ověřování Azure AD'
description: Poradce při potížích s ověřovacími klienty služby Azure AD brány VPN P2S
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 8871e92f0911c4d3cbcc1772bef1daeb5c70b5d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74151968"
---
# <a name="troubleshoot-an-azure-ad-authentication-vpn-client"></a>Poradce při potížích s klientem VPN ověřování Azure AD

Tento článek vám pomůže vyřešit potíže s klientem VPN pro připojení k virtuální síti pomocí ověřování VPN bodu na místo a ověřování Azure Active Directory.

## <a name="view-status-log"></a><a name="status"></a>Zobrazit protokol stavu

Zobrazení protokolu stavu chybových zpráv.

![Protokoly](./media/troubleshoot-ad-vpn-client/1.png)

1. Klepnutím na ikonu šipek v pravém dolním rohu okna klienta zobrazte **protokoly stavu**.
2. Zkontrolujte, zda protokoly chyby, které mohou znamenat problém.
3. Chybové zprávy jsou zobrazeny červeně.

## <a name="clear-sign-in-information"></a><a name="clear"></a>Vymazat přihlašovací informace

Vymažte přihlašovací informace.

![přihlášení](./media/troubleshoot-ad-vpn-client/2.png)

1. Vyberte ... vedle profilu, který chcete řešit. Vyberte **konfigurovat -> vymazat uložený účet**.
2. Vyberte **Uložit**.
3. Pokuste se připojit.
4. Pokud se připojení stále nezdaří, pokračujte k další části.

## <a name="run-diagnostics"></a><a name="diagnostics"></a>Spuštění diagnostiky

Spusťte diagnostiku v klientovi VPN.

![Diagnostika](./media/troubleshoot-ad-vpn-client/3.png)

1. Klikněte na **...** vedle profilu, na kterém chcete spustit diagnostiku. Vyberte **diagnostikovat -> spustit diagnózu**.
2. Klient spustí řadu testů a zobrazí výsledek testu

   * Přístup k Internetu – zkontroluje, zda má klient připojení k Internetu.
   * Pověření klienta – zkontrolujte, jestli je dostupný koncový bod ověřování služby Azure Active Directory.
   * Server Resolvable – kontaktuje DNS server, aby přelože IP adresu nakonfigurovaného serveru VPN
   * Server Dosažitelný – Zkontroluje, zda server VPN reaguje nebo ne
3. Pokud některý z testů selže, obraťte se na správce sítě a problém vyřešte.
4. V další části se zobrazí, jak shromažďovat protokoly, v případě potřeby.

## <a name="collect-client-log-files"></a><a name="logfiles"></a>Shromažďování souborů protokolu klienta

Shromážděte soubory protokolu klienta VPN. Soubory protokolu mohou být zaslány na podporu / správce metodou podle vašeho výběru. Například e-mail.

1. Klikněte na "..." vedle profilu, na kterém chcete spustit diagnostiku. Vyberte **možnost Diagnostikovat -> Zobrazit adresář protokolů**.

   ![zobrazit protokoly](./media/troubleshoot-ad-vpn-client/4.png)
2. Průzkumník Windows se otevře do složky, která obsahuje soubory protokolu.

   ![zobrazit soubor](./media/troubleshoot-ad-vpn-client/5.png)

## <a name="next-steps"></a>Další kroky

Další informace najdete [v tématu Vytvoření klienta Služby Azure Active Directory pro připojení P2S Open VPN, která používají ověřování Azure AD](openvpn-azure-ad-tenant.md).