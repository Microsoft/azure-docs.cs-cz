---
title: Řešení potíží s připojením k doméně pomocí Azure AD Domain Services | Microsoft Docs
description: Naučte se řešit běžné problémy při pokusu o připojení k doméně virtuální počítač nebo připojení aplikace k Azure Active Directory Domain Services a nemůžete se připojit nebo ověřit ve spravované doméně.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 86d6ca79a12e4706f558e92c3c83c5bddaa99b3c
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2020
ms.locfileid: "96618599"
---
# <a name="troubleshoot-domain-join-problems-with-an-azure-active-directory-domain-services-managed-domain"></a>Řešení potíží s připojením k doméně pomocí spravované domény Azure Active Directory Domain Services

Když se pokusíte připojit k virtuálnímu počítači nebo připojit aplikaci k spravované doméně Azure Active Directory Domain Services (Azure služba AD DS), může se zobrazit chyba, že to nemůžete udělat. Pokud chcete řešit problémy s připojením k doméně, přečtěte si, na kterých z následujících bodů máte problém:

* Pokud se výzva k ověření nezobrazuje, virtuální počítač nebo aplikace se nemůže připojit ke spravované doméně Azure služba AD DS.
    * Začněte řešit [potíže s připojením pro připojení k doméně](#connectivity-issues-for-domain-join).
* Pokud při ověřování dojde k chybě, připojení ke spravované doméně je úspěšné.
    * Začněte řešit [problémy související s přihlašovacími údaji během připojení k doméně](#credentials-related-issues-during-domain-join).

## <a name="connectivity-issues-for-domain-join"></a>Problémy s připojením k doméně – připojení

Pokud virtuální počítač nemůže najít spravovanou doménu, obvykle se jedná o síťové připojení nebo problém s konfigurací. Pokud chcete tento problém najít a vyřešit, přečtěte si následující postup pro řešení potíží:

1. Zajistěte, aby byl virtuální počítač připojený ke stejné nebo partnerské virtuální síti jako spravovaná doména. V takovém případě se virtuální počítač nemůže k doméně najít a připojit se, aby se mohl připojit.
    * Pokud virtuální počítač není připojený ke stejné virtuální síti, zkontrolujte, jestli je *aktivní* nebo *připojené* připojení VPN peering nebo VPN, aby se mohl správně Flow provozovat.
1. Zkuste použít příkaz k otestování domény pomocí názvu domény spravované domény, například `ping aaddscontoso.com` .
    * Pokud se odpověď protokolu příkazového testu nezdařila, zkuste provést příkaz k otestování adresy IP pro doménu zobrazenou na stránce Přehled na portálu spravované domény, například `ping 10.0.0.4` .
    * Pokud můžete úspěšně odeslat příkaz k otestování IP adresy, ale ne k doméně, je možné, že je služba DNS nesprávně nakonfigurovaná. Ujistěte se, že jste [nakonfigurovali servery DNS spravované domény pro virtuální síť][configure-dns].
1. Zkuste vyprázdnit mezipaměť překladače DNS na virtuálním počítači, třeba `ipconfig /flushdns` .

### <a name="network-security-group-nsg-configuration"></a>Konfigurace skupiny zabezpečení sítě (NSG)

Při vytváření spravované domény se vytvoří také skupina zabezpečení sítě s příslušnými pravidly pro úspěšnou operaci domény. Pokud upravujete nebo vytváříte další pravidla skupiny zabezpečení sítě, možná nebudete záměrně blokovat porty požadované pro Azure služba AD DS k poskytování služeb připojení a ověřování. Tato pravidla skupiny zabezpečení sítě mohou způsobit problémy, jako je například synchronizace hesel není dokončená, uživatelé se nebudou moci přihlásit ani problémy s připojením k doméně.

Pokud budete mít i nadále problémy s připojením, přečtěte si následující postup pro řešení potíží:

1. Ověřte stav spravované domény v Azure Portal. Pokud máte výstrahu pro *AADDS001*, pravidlo skupiny zabezpečení sítě blokuje přístup.
1. Zkontrolujte [požadované porty a pravidla skupiny zabezpečení sítě][network-ports]. Ujistěte se, že se žádná pravidla skupiny zabezpečení sítě používaná u virtuálního počítače nebo virtuální sítě, ze které se připojujete, zablokují tyto síťové porty.
1. Jakmile se vyřeší jakékoli problémy s konfigurací skupiny zabezpečení sítě, výstraha *AADDS001* zmizí ze stránky stav přibližně 2 hodiny. Když je teď k dispozici připojení k síti, zkuste znovu připojit virtuální počítač k doméně.

## <a name="credentials-related-issues-during-domain-join"></a>Problémy související s přihlašovacími údaji během připojení k doméně

Pokud se zobrazí dialogové okno s výzvou k zadání přihlašovacích údajů pro připojení ke spravované doméně, virtuální počítač se může připojit k doméně pomocí virtuální sítě Azure. Proces připojení k doméně se nezdařil při ověřování v doméně nebo autorizaci k dokončení procesu připojení k doméně pomocí přihlašovacích údajů.

Pokud chcete řešit problémy související s přihlašovacími údaji, přečtěte si následující postup pro řešení potíží:

1. Zkuste zadat přihlašovací údaje pomocí formátu UPN, například `dee@contoso.onmicrosoft.com` . Ujistěte se, že je tento hlavní název uživatele ve službě Azure AD správně nakonfigurovaný.
    * Pokud má váš tenant více uživatelů se stejnou předponou hlavního názvu uživatele (UPN), nebo pokud je předpona hlavního názvu uživatele delší, je možné, že se *sAMAccountName* pro váš účet vygeneruje automaticky. Proto se formát *sAMAccountName* pro váš účet může lišit od toho, co očekáváte nebo používáte ve vaší místní doméně.
1. Zkuste použít přihlašovací údaje pro uživatelský účet, který je součástí spravované domény pro připojení virtuálních počítačů ke spravované doméně.
1. Ujistěte se, že jste [povolili synchronizaci hesel][enable-password-sync] a pro dokončení počáteční synchronizace hesla čekaly dostatečně dlouho.

## <a name="next-steps"></a>Další kroky

Hlubší porozumění procesům služby Active Directory v rámci operace připojení k doméně najdete v tématu [problémy s připojením a ověřením][join-authentication-issues].

Pokud stále máte problémy s připojením k VIRTUÁLNÍmu počítači ke spravované doméně, [Najděte nápovědu a otevřete lístek podpory pro Azure Active Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[enable-password-sync]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-dns]: tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network

<!-- EXTERNAL LINKS -->
[join-authentication-issues]: /previous-versions/windows/it-pro/windows-2000-server/cc961817(v=technet.10)
