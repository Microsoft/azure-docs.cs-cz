---
title: Poradce při potížích s připojením k doméně se službou Azure AD Domain Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak řešit běžné problémy při pokusu o připojení k virtuálnímu počítači nebo připojení aplikace ke službě Azure Active Directory Domain Services a nemůžete se připojit nebo ověřit ke spravované doméně.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: f187dba4eace61695a72e4b7b08731e65ff0d7f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299104"
---
# <a name="troubleshoot-domain-join-problems-with-an-azure-ad-domain-services-managed-domain"></a>Poradce při potížích se připojením k doméně se spravovanou doménou služby Azure AD Domain Services

Když se pokusíte připojit k virtuálnímu počítači (VM) nebo připojit aplikaci ke spravované doméně služby Azure Active Directory Domain Services (AD DS), může se stát chyba, že to nemůžete udělat. Chcete-li vyřešit problémy s připojením k doméně, zkontrolujte, u kterého z následujících bodů máte problém:

* Pokud neobdržíte výzvu k ověření, virtuální počítač nebo aplikace se nemůže připojit ke spravované doméně Azure AD DS.
    * Začněte řešit [problémy s připojením pro připojení k doméně](#connectivity-issues-for-domain-join).
* Pokud se během ověřování zobrazí chyba, připojení ke spravované doméně Azure AD DS je úspěšné.
    * Začněte řešit [problémy související s pověřeními během připojení k doméně](#credentials-related-issues-during-domain-join).

## <a name="connectivity-issues-for-domain-join"></a>Problémy s připojením pro připojení k doméně

Pokud virtuální počítač nemůže najít spravovanou doménu Azure AD DS, obvykle dochází k problému se síťovým připojením nebo konfigurací. Chcete-li problém vyhledat a vyřešit, přečtěte si následující kroky řešení potíží:

1. Ujistěte se, že virtuální počítač je připojený ke stejnému nebo partnerské virtuální síti, která je povolená pro Azure AD DS. Pokud ne, virtuální ho disponuje a nemůže najít doménu a připojit se k ní, aby se mohl připojit.
    * Pokud virtuální počítač není připojený ke stejné virtuální síti, zkontrolujte, že partnerský vztah virtuální sítě nebo připojení VPN je *aktivní* nebo *připojené,* aby se provoz mohl správně tokovat.
1. Zkuste příkazem ping příkazem ping na doménu pomocí názvu `ping aaddscontoso.com`domény spravované domény Azure AD DS, jako je například .
    * Pokud se odezva příkazu ping nezdaří, zkuste ping ip adresy pro doménu zobrazenou na stránce s `ping 10.0.0.4`přehledem na portálu pro vaši spravovanou doménu Azure AD DS, například .
    * Pokud můžete úspěšně příkazem ping příkazem PING na adresu IP, ale nikoli na doménu, může být služba DNS nesprávně nakonfigurována. Ujistěte se, že jste nakonfigurovali servery DNS spravované domény Azure AD DS pro virtuální síť.
1. Zkuste vyprázdnit mezipaměť překladače DNS `ipconfig /flushdns`ve virtuálním počítači, například .

### <a name="network-security-group-nsg-configuration"></a>Konfigurace skupiny zabezpečení sítě (NSG)

Když vytvoříte spravovanou doménu Azure AD DS, vytvoří se také skupina zabezpečení sítě s příslušnými pravidly pro úspěšnou operaci domény. Pokud upravíte nebo vytvoříte další pravidla skupiny zabezpečení sítě, můžete neúmyslně zablokuje porty požadované pro Azure AD DS poskytovat připojení a ověřování služby. Tato pravidla skupiny zabezpečení sítě mohou způsobit problémy, jako je například nedokončení synchronizace hesel, uživatelé, kteří se nemohou přihlásit, nebo problémy s připojením k doméně.

Pokud máte i nadále problémy s připojením, přečtěte si následující kroky řešení potíží:

1. Zkontrolujte stav spravované domény Azure AD DS na webu Azure Portal. Pokud máte výstrahu pro *AADDS001*, pravidlo skupiny zabezpečení sítě blokuje přístup.
1. Zkontrolujte [požadované porty a pravidla skupiny zabezpečení sítě][network-ports]. Ujistěte se, že žádná pravidla skupiny zabezpečení sítě použitá pro virtuální počítač nebo virtuální síť, kterou připojujete z bloku těchto síťových portů.
1. Po vyřešení všech problémů s konfigurací skupiny zabezpečení sítě zmizí výstraha *AADDS001* ze stránky stavu přibližně za 2 hodiny. Pokud je teď k dispozici připojení k síti, zkuste se k virtuálnímu virtuálnímu virtuálnímu zařízení připojit znovu.

## <a name="credentials-related-issues-during-domain-join"></a>Problémy související s pověřeními během připojení k doméně

Pokud se zobrazí dialogové okno, které požádá o pověření pro připojení ke spravované doméně Azure AD DS, virtuální počítač se může připojit k doméně pomocí virtuální sítě Azure. Proces připojení k doméně se nezdaří při ověřování domény nebo autorizaci k dokončení procesu připojení k doméně pomocí pověření.

Chcete-li vyřešit problémy související s pověřeními, přečtěte si následující kroky řešení potíží:

1. Zkuste pomocí formátu UPN určit přihlašovací `dee@aaddscontoso.onmicrosoft.com`údaje, například . Ujistěte se, že tento upn je správně nakonfigurovaný ve službě Azure AD.
    * *Název SAMAccountName* pro váš účet může být automaticky vygenerován, pokud je ve vašem tenantovi více uživatelů se stejnou předponou UPN nebo pokud je předpona hlavního názvu uživatele příliš dlouhá. Proto se formát *SAMAccountName* pro váš účet může lišit od toho, co očekáváte nebo používáte v místní doméně.
1. Zkuste použít přihlašovací údaje pro uživatelský účet, který je součástí spravované domény Azure AD DS pro připojení virtuálních počítače ke spravované doméně.
1. Ujistěte se, že jste [povolili synchronizaci hesel][enable-password-sync] a čekali jste dostatečně dlouho na dokončení počáteční synchronizace hesel.

## <a name="next-steps"></a>Další kroky

Pro hlubší pochopení procesů služby Active Directory jako součást operace připojení k doméně naleznete v [tématu Join and authentication issues][join-authentication-issues].

Pokud máte pořád problémy s připojením virtuálního počítače ke spravované doméně Azure AD DS, [najděte nápovědu a otevřete lístek podpory pro Azure Active Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[enable-password-sync]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[join-authentication-issues]: /previous-versions/windows/it-pro/windows-2000-server/cc961817(v=technet.10)
