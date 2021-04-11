---
title: Nastavení protokolu Kerberos služba KDC (Key Distribution Center) virtuální plocha Windows proxy serveru – Azure
description: Jak nastavit fond hostitelů pro virtuální počítače s Windows tak, aby používal proxy služba KDC (Key Distribution Center) protokolu Kerberos.
author: Heidilohr
ms.topic: how-to
ms.date: 03/20/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: a1e6d2f4fc0444be364ce3c07c14f6408b3f19fe
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106446906"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy-preview"></a>Konfigurace proxy služba KDC (Key Distribution Center) protokolu Kerberos (Preview)

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučujeme ji používat pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Zákazníkům s vědomím zabezpečení, jako jsou finanční nebo státní organizace, se často přihlásí pomocí čipových karet. Pomocí čipových karet se nasazení zvyšuje na bezpečnější, protože vyžaduje vícefaktorové ověřování (MFA). Pro část RDP relace virtuálního počítače se systémem Windows ale čipové karty vyžadují přímé připojení nebo "na základě" pohledu na uživatele "s řadičem domény služby Active Directory (AD) pro ověřování protokolem Kerberos. Bez tohoto přímého připojení se uživatelé nemůžou do sítě organizace automaticky přihlašovat ze vzdálených připojení. Uživatelé v nasazení virtuálních počítačů s Windows můžou použít službu KDC proxy Service k proxy tomuto provozu ověřování a přihlásit se vzdáleně. Proxy služby KDC umožňuje ověřování pro protokol RDP (Remote Desktop Protocol) relace virtuální plochy Windows, takže se uživatel může bezpečně přihlásit. Díky tomu je práce z domova mnohem jednodušší a umožňuje určitým scénářům zotavení po havárii plynule běžet.

Nastavení serveru proxy služby KDC obvykle zahrnuje přiřazení role brány systému Windows Server v systému Windows Server 2016 nebo novějším. Jak se k přihlášení k virtuálnímu počítači s Windows používá role služby Vzdálená plocha? Abychom na to odpověděli, Pojďme se podívat na komponenty.

Existují dvě součásti pro službu Virtual Desktop systému Windows, které je třeba ověřit:

- Informační kanál klienta virtuální plochy Windows, který uživatelům poskytuje seznam dostupných ploch nebo aplikací, ke kterým mají přístup. K tomuto procesu ověřování dojde v Azure Active Directory, což znamená, že tato součást není zaměřená na tento článek.
- Relace protokolu RDP, která je výsledkem výběru některého z dostupných prostředků uživatelem. Tato součást používá ověřování pomocí protokolu Kerberos a vyžaduje pro vzdálené uživatele proxy služby KDC.

V tomto článku se dozvíte, jak nakonfigurovat informační kanál v klientovi virtuální plochy Windows v Azure Portal. Pokud se chcete dozvědět, jak nakonfigurovat roli Brána VP, přečtěte si téma [nasazení Brána VP role](/windows-server/remote/rd-gateway-role).

## <a name="requirements"></a>Požadavky

Pokud chcete nakonfigurovat hostitele relace virtuálních počítačů s Windows pomocí služby KDC proxy, budete potřebovat následující věci:

- Přístup k Azure Portal a účtu správce Azure.
- Na vzdálených klientských počítačích musí být spuštěný systém Windows 10 nebo Windows 7 a musí být nainstalovaný [klient Windows Desktop](/windows-server/remote/remote-desktop-services/clients/windowsdesktop) .
- Je nutné, aby byl na vašem počítači již nainstalován proxy služby KDC. Další informace o tom, jak to provést, najdete v tématu [Nastavení role Brána VP pro virtuální počítač s Windows](rd-gateway-role.md).
- Operační systém počítače musí být Windows Server 2016 nebo novější.

Jakmile budete mít jistotu, že splňujete tyto požadavky, budete připraveni začít.

## <a name="how-to-configure-the-kdc-proxy"></a>Jak nakonfigurovat proxy služby KDC

Konfigurace proxy služby KDC:

1. Přihlaste se k Azure Portal jako správce.

2. Přejít na stránku virtuální počítač s Windows.

3. Vyberte fond hostitelů, pro který chcete povolit službu KDC proxy, a pak vyberte **Vlastnosti protokolu RDP**.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky stránky Azure Portal znázorňující uživatele, který vybírá fondy hostitelů, potom název ukázkového fondu hostitelů a pak vlastnosti protokolu RDP.](media/rdp-properties.png)

4. Vyberte kartu **Upřesnit** a pak zadejte hodnotu v následujícím formátu bez mezer:

    
    > kdcproxyname: s:\<fqdn\>
    

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky zobrazující vybranou kartu Upřesnit s hodnotou zadanou v kroku 4.](media/advanced-tab-selected.png)

5. Vyberte **Uložit**.

6. Vybraný fond hostitelů by teď měl začít vystavovat soubory připojení RDP, které obsahují hodnotu kdcproxyname, kterou jste zadali v kroku 4.

## <a name="next-steps"></a>Další kroky

Informace o tom, jak spravovat vzdálenou plochu serveru proxy služby KDC a přiřadit Brána VP roli, najdete v tématu [nasazení Brána VP role](rd-gateway-role.md).

Pokud vás zajímá, jak vaše proxy servery služby KDC škálovat, přečtěte si, jak nastavit vysokou dostupnost pro proxy služby KDC s [vysokou dostupností na webu a na webu brány pro vzdálené plochy](/windows-server/remote/remote-desktop-services/rds-rdweb-gateway-ha).
