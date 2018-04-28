---
title: Verze služby Azure MFA a spotřeba plány | Microsoft Docs
description: Informace o vícefaktorového ověřování klienta a různé metody a verze, které jsou k dispozici. Podrobnosti o jednotlivých plánu spotřeby
keywords: ''
services: multi-factor-authentication
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.assetid: ''
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: joflore
ms.openlocfilehash: 5fbc42d11ccb9a36ce146deca173526536c9fc9e
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-get-azure-multi-factor-authentication"></a>Jak získat Azure Multi-Factor Authentication

Pokud jde o ochranu svých účtů, musí být dvoustupňové ověření standardní celé organizaci. Tato funkce je obzvláště důležité pro účty pro správu, které mají privilegovaný přístup k prostředkům. Z tohoto důvodu společnost Microsoft nabízí funkce základní dvoustupňové ověření do služeb Office 365 a Azure správci nejsou zpoplatněné. Pokud chcete upgradovat funkce pro vaši správci nebo rozšířit dvoustupňové ověřování s ostatními uživatelů, si můžete zakoupit Azure Multi-Factor Authentication. 

Tento článek vysvětluje rozdíly mezi verzemi, které nabízí pro správce a na plnou verzi Azure MFA. Pokud jste připravení nasadit kompletní Azure MFA nabídka, v oddílu popisuje možnosti implementace a jak Microsoft vypočítá spotřeby.


>[!IMPORTANT]
>Tento článek slouží jako průvodce vám pomůžou pochopit různé způsoby, jak zakoupit Azure Multi-Factor Authentication. Pro konkrétní podrobnosti o cenách a fakturace, by měla vždycky odkazujete [Multi-Factor Authentication stránce s cenami](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="available-versions-of-azure-multi-factor-authentication"></a>K dispozici verze služby Azure Multi-Factor Authentication

Následující tabulka popisuje rozdíly mezi tři verze služby Multi-Factor authentication:

| Verze | Popis |
| --- | --- |
| Služba Multi-Factor Authentication (vícefaktorové ověřování) pro Office 365 |Tato verze funguje výhradně u aplikací Office 365 a je spravovat z portálu Office 365. Správci můžou [zabezpečení prostředků Office 365 s dvoustupnovym overovanim](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6). Tato verze je součástí předplatné služeb Office 365. |
| Služba Multi-Factor Authentication pro správce Azure AD | Uživatelé s přiřazenou roli globálního správce v klienty Azure AD můžete povolit dvoustupňové ověřování pro své účty Azure AD globálního správce bez dalších poplatků.|
| Azure Multi-Factor Authentication | Často označuje jako "úplná" verze, Azure Multi-Factor Authentication nabízí nejkomplexnější sadu funkcí. Poskytuje další možnosti konfigurace prostřednictvím [portál Azure](https://portal.azure.com), pokročilé vytváření sestav a podpora pro řadu místní i cloudové aplikace. Azure Multi-Factor Authentication je součástí [plánů Azure Active Directory Premium](https://www.microsoft.com/cloud-platform/azure-active-directory-features) a [Enterprise Mobility + Security plány](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-pricing)a dá se nasadit buď v cloudu nebo místně. |

## <a name="feature-comparison-of-versions"></a>Porovnání funkcí verze
Následující tabulka obsahuje seznam funkcí, které jsou k dispozici v různých verzích Azure Multi-Factor Authentication.

> [!NOTE]
> Tato tabulka porovnání popisuje funkce, které jsou součástí jednotlivých verzí služby Multi-Factor Authentication. Pokud máte plnou verzi služby Azure Multi-Factor Authentication, některé funkce nemusí být k dispozici v závislosti na tom, zda používáte [MFA v cloudu nebo MFA místní](concept-mfa-whichversion.md).


| Funkce | Služba Multi-Factor Authentication (vícefaktorové ověřování) pro Office 365 | Služba Multi-Factor Authentication pro správce Azure AD | Azure Multi-Factor Authentication |
| --- |:---:|:---:|:---:|
| Chránit účty správce Azure AD pomocí vícefaktorového ověřování |● |● (pouze účty Azure AD globálního správce) |● |
| Mobilní aplikace jako druhý faktor |● |● |● |
| Telefonní hovor jako druhý faktor |● |● |● |
| Služby SMS jako druhý faktor |● |● |● |
| Hesla aplikací pro klienty, kteří nepodporují MFA |● |● |● |
| Správce kontrolu nad metody ověření |● |● |● |
| Chránit bez oprávnění správce. účty s MFA |● (pouze pro aplikace Office 365) | |● |
| Režim kódu PIN | | |● |
| Výstraha podvodů | | |● |
| Sestavy MFA | | |● |
| Jednorázové potlačení | | |● |
| Vlastní přivítání pro telefonní hovory | | |● |
| ID vlastní volajícího pro telefonní hovory | | |● |
| Důvěryhodné IP adresy | | |● |
| Zapamatovat MFA pro důvěryhodná zařízení |● |● |● |
| MFA SDK | | |● (zastaralé) | 
| MFA pro místní aplikace | | |● |

## <a name="how-to-turn-on-azure-multi-factor-authentication-for-azure-ad-administrators"></a>Zapnutí ověřování Azure Multi-Factor Authentication pro správce Azure AD
Uživatelé s přiřazenou roli globálního správce v klienty Azure AD můžete povolit dvoustupňové ověřování pro své účty Azure AD globálního správce bez dalších poplatků. Pokud používáte Account Microsoft, můžete zaregistrovat u služby Multi-Factor authentication [zde](https://support.microsoft.com/en-us/help/12408/microsoft-account-about-two-step-verification). Pokud nepoužíváte Account Microsoft, zapnout vícefaktorové ověřování pro globální správci [zde](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states).

## <a name="how-to-get-azure-multi-factor-authentication"></a>Jak získat Azure Multi-Factor Authentication
Pokud chcete úplné funkce nabízené službou Azure Multi-Factor Authentication, máte několik možností:

### <a name="option-1---mfa-licenses"></a>Možnost 1 - licence MFA

Zakupte licence ověřování Azure Multi-Factor Authentication a přiřadit uživatelům v Azure Active Directory. 

Pokud použijete tuto možnost, vytvořte poskytovatele Azure Multi-Factor Authentication pouze v případě budete muset zadat dvoustupňové ověřování pro některé uživatele, kteří nemají licence. Jinak může být fakturuje dvakrát.

### <a name="option-2---bundled-licenses-that-include-mfa"></a>Možnost 2 - dodávat licencí, které zahrnují vícefaktorového ověřování

Nákup licencí, které zahrnují Azure Multi-Factor Authentication, jako je Azure Active Directory Premium nebo Enterprise Mobility + Security a přiřadit jim uživatelům v Azure Active Directory. 

Pokud použijete tuto možnost, měli byste vytvořit poskytovatele Azure Multi-Factor Authentication, pouze v případě, že budete taky muset zadat dvoustupňové ověřování pro některé uživatele, kteří nemají licence. Jinak může být fakturuje dvakrát. 

### <a name="option-3---mfa-consumption-based-model"></a>Možnost 3 - vícefaktorového ověřování na základě spotřeby modelu

Vytvořte poskytovatele Azure Multi-Factor Authentication v rámci předplatného Azure. Zprostředkovatele Azure MFA jsou prostředky Azure, které se účtují podle vaší smlouvy Enterprise, Azure peněžních závazků nebo platební karty jako všechny ostatní prostředky služby Azure. Tyto zprostředkovatele lze vytvořit pouze v úplné Azure předplatných, mimo jiné předplatná Azure, které mají 0 $ útrat. Omezené předplatných se vytvoří při aktivaci licencí, jako je v možnosti 1 a 2. 

Pokud používáte poskytovatele Azure Multi-Factor Authentication, existují dva modely využití k dispozici, který se účtují prostřednictvím vašeho předplatného Azure:  

1. **Per Enabled User** – podnikům, které chcete zapnout dvoustupňové ověřování pro pevný počet uživatelů, kteří potřebují pravidelně ověřování. Uživatelská fakturace vychází počet uživatelů v klientovi Azure AD a Azure MFA serveru povolené pro MFA. Pokud uživatelé jsou povolené pro MFA v obou Azure AD a Azure MFA serveru a je povolená synchronizace domény (Azure AD Connect) a potom jsme počet větší sadu uživatelů systému. Pokud není povolená synchronizace domény, pak jsme počet součet všechny uživatele s povoleným vícefaktorového ověřování ve službě Azure AD a Azure MFA serveru. Fakturace je účtovány poměrnou částí a obchodu Spojených států systému denně. 

  > [!NOTE]
  > Fakturace Příklad 1: máte 5 000 uživatele s povoleným MFA ještě dnes. Systém MFA vydělí toto číslo 31 a 161.29 uživatelé sestavy pro daný den. Je potřeba povolit 15 více uživatelů, aby systém MFA sestav 161.77 uživatelů pro daný den zítra. Na konci fakturačního cyklu celkový počet uživatelů, na které se účtují u vašeho předplatného Azure až přidá přibližně 5 000. 
  >
  > Fakturace příklad 2: máte směs uživatelé s licencí a uživatele bez, takže budete mít zprostředkovatele Azure MFA na uživatele a společně tvoří rozdíl. Existují 4500 Enterprise Mobility + zabezpečení licencí na klientovi, ale 5 000 uživatele s povoleným pro MFA. Vaše předplatné Azure je účtovány poplatky za 500 uživatelů, účtovány poměrnou částí a každý den jako 16.13 uživatele. 

2. **Za ověření** – podnikům, které chcete zapnout dvoustupňové ověřování pro velkou skupinu uživatelů, kteří potřebují zřídka ověřování. Fakturace je založen na řadě dvoustupňové ověření požadavků, a to bez ohledu na to, jestli tyto ověření úspěšné nebo byl odepřen. Tato fakturace se zobrazí na vaší příkaz Azure využití v balíčky 10 ověřování a hlásí denně. 

  > [!NOTE]
  > Fakturace příklad 3: v současné době služby Azure MFA obdržela 3,105 dvoustupňové ověření požadavků. Vaše předplatné Azure se fakturuje 310.5 sad ověřování. 

Je důležité si uvědomit, že může mít licence Azure MFA, ale stále získat účtuje konfiguraci na základě spotřeby. Pokud nastavíte zprostředkovatele Azure MFA na ověřování, fakturuje se pro každý požadavek dvoustupňové ověření, včetně těch, které se provádí uživatelé, kteří mají licence. Pokud nastavíte zprostředkovatele Azure MFA na uživatele v doméně, který není přidružený ke klientovi Azure AD, vám fakturují za povoleného uživatele i v případě, že uživatelé mají licence na Azure AD. 

## <a name="next-steps"></a>Další postup

- Podrobnosti o cenách najdete v části [ceník služby Azure MFA](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

- Vyberte, jestli chcete nasadit Azure MFA [v cloudu nebo místně](concept-mfa-whichversion.md)
