---
title: Řešení potíží s připojením k doméně pomocí Azure AD Domain Services | Microsoft Docs
description: Naučte se řešit běžné problémy při pokusu o připojení k doméně virtuální počítač nebo připojení aplikace k Azure Active Directory Domain Services a nemůžete se připojit nebo ověřit ve spravované doméně.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: 1016fbc1478ec713d50a2f04bcc80d08288b03f3
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827243"
---
# <a name="troubleshoot-domain-join-problems-with-an-azure-ad-domain-services-managed-domain"></a>Řešení potíží s připojením k doméně pomocí spravované domény Azure AD Domain Services

Když se pokusíte připojit k virtuálnímu počítači nebo připojit aplikaci k spravované doméně Azure Active Directory Domain Services (služba AD DS), může se zobrazit chyba, že to nemůžete udělat. Pokud chcete řešit problémy s připojením k doméně, přečtěte si, na kterých z následujících bodů máte problém:

* Pokud se výzva k ověření nezobrazuje, virtuální počítač nebo aplikace se nemůže připojit ke spravované doméně Azure služba AD DS.
    * Začněte řešit [potíže s připojením pro připojení k doméně](#connectivity-issues-for-domain-join).
* Pokud při ověřování dojde k chybě, připojení ke spravované doméně Azure služba AD DS úspěšné.
    * Začněte řešit [problémy související s přihlašovacími údaji během připojení k doméně](#credentials-related-issues-during-domain-join).

## <a name="connectivity-issues-for-domain-join"></a>Problémy s připojením k doméně – připojení

Pokud virtuální počítač nemůže najít spravovanou doménu Azure služba AD DS, obvykle se jedná o síťové připojení nebo problém s konfigurací. Pokud chcete tento problém najít a vyřešit, přečtěte si následující postup pro řešení potíží:

1. Ujistěte se, že je virtuální počítač připojený ke stejné nebo partnerské virtuální síti, která je povolená pro Azure služba AD DS. V takovém případě se virtuální počítač nemůže k doméně najít a připojit se, aby se mohl připojit.
    * Pokud virtuální počítač není připojený ke stejné virtuální síti, zkontrolujte, jestli je *aktivní* nebo *připojené* připojení VPN peering nebo VPN, aby se mohl správně Flow provozovat.
1. Zkuste použít příkaz k otestování domény pomocí názvu domény spravované domény Azure služba AD DS, například `ping contoso.com`.
    * Pokud se odpověď protokolu příkazového testu nezdařila, zkuste na portálu ověřit IP adresy pro doménu zobrazenou na stránce Přehled na portálu spravované domény Azure služba AD DS, například `ping 10.0.0.4`.
    * Pokud můžete úspěšně odeslat příkaz k otestování IP adresy, ale ne k doméně, je možné, že je služba DNS nesprávně nakonfigurovaná. Ujistěte se, že jste nakonfigurovali servery DNS spravované domény Azure služba AD DS pro virtuální síť.
1. Zkuste vyprázdnit mezipaměť překladače DNS na virtuálním počítači, například `ipconfig /flushdns`.

### <a name="network-security-group-nsg-configuration"></a>Konfigurace skupiny zabezpečení sítě (NSG)

Když vytváříte spravovanou doménu Azure služba AD DS, vytvoří se taky skupina zabezpečení sítě s příslušnými pravidly pro úspěšnou operaci domény. Pokud upravíte nebo vytvoříte další pravidla skupiny zabezpečení sítě, možná nebudete mít k dispozici neúmyslné blokování portů, které služba Azure služba AD DS potřebuje k poskytování služeb připojení a ověřování. Tato pravidla skupiny zabezpečení sítě mohou způsobit problémy, jako je například synchronizace hesel není dokončená, uživatelé se nebudou moci přihlásit ani problémy s připojením k doméně.

Pokud budete mít i nadále problémy s připojením, přečtěte si následující postup pro řešení potíží:

1. V Azure Portal se podívejte na stav vaší spravované domény Azure služba AD DS. Pokud máte výstrahu pro *AADDS001*, pravidlo skupiny zabezpečení sítě blokuje přístup.
1. Zkontrolujte [požadované porty a pravidla skupiny zabezpečení sítě][network-ports]. Ujistěte se, že se žádná pravidla skupiny zabezpečení sítě používaná u virtuálního počítače nebo virtuální sítě, ze které se připojujete, zablokují tyto síťové porty.
1. Jakmile se vyřeší jakékoli problémy s konfigurací skupiny zabezpečení sítě, výstraha *AADDS001* zmizí ze stránky stav přibližně 2 hodiny. Když je teď k dispozici připojení k síti, zkuste znovu připojit virtuální počítač k doméně.

## <a name="credentials-related-issues-during-domain-join"></a>Problémy související s přihlašovacími údaji během připojení k doméně

Pokud se zobrazí dialogové okno s výzvou k zadání přihlašovacích údajů pro připojení ke spravované doméně Azure služba AD DS, virtuální počítač se může připojit k doméně pomocí virtuální sítě Azure. Proces připojení k doméně se nezdařil při ověřování v doméně nebo autorizaci k dokončení procesu připojení k doméně pomocí přihlašovacích údajů.

Pokud chcete řešit problémy související s přihlašovacími údaji, přečtěte si následující postup pro řešení potíží:

1. Zkuste zadat přihlašovací údaje pomocí formátu UPN, například `dee@contoso.onmicrosoft.com`. Ujistěte se, že je tento hlavní název uživatele ve službě Azure AD správně nakonfigurovaný.
    * Pokud má váš tenant více uživatelů se stejnou předponou hlavního názvu uživatele (UPN), nebo pokud je předpona hlavního názvu uživatele delší, je možné, že se *sAMAccountName* pro váš účet vygeneruje automaticky. Proto se formát *sAMAccountName* pro váš účet může lišit od toho, co očekáváte nebo používáte ve vaší místní doméně.
1. Zkuste použít přihlašovací údaje pro uživatelský účet, který patří do skupiny Domain *Administrators služby AAD* pro připojení virtuálních počítačů k spravované doméně Azure služba AD DS.
1. Ujistěte se, že jste [povolili synchronizaci hesel][enable-password-sync] a pro dokončení počáteční synchronizace hesla čekaly dostatečně dlouho.

## <a name="next-steps"></a>Další kroky

Hlubší porozumění procesům služby Active Directory v rámci operace připojení k doméně najdete v tématu [problémy s připojením a ověřením][join-authentication-issues].

Pokud stále máte problémy s připojením k VIRTUÁLNÍmu počítači do spravované domény Azure služba AD DS, [Najděte nápovědu a otevřete lístek podpory pro Azure Active Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[enable-password-sync]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[join-authentication-issues]: /previous-versions/windows/it-pro/windows-2000-server/cc961817(v=technet.10)
