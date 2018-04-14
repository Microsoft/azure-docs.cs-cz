---
title: "Ověřování identit bez hesel prostřednictvím Windows Hello pro firmy s Azure AD | Microsoft Docs"
description: "Obsahuje přehled Windows Hello pro firmy a další informace o nasazení Windows Hello pro firmy."
services: active-directory
documentationcenter: 
author: femila
manager: mtillman
editor: 
tags: azure-classic-portal
ms.assetid: f907bb90-8776-46ca-9e12-279949af66ff
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: markvi
ms.openlocfilehash: 22534cc5f7f2de235bc1f1212c63ea227083c5a4
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2017
---
# <a name="authenticating-identities-without-passwords-through-windows-hello-for-business"></a>Ověřování identit bez hesel prostřednictvím Windows Hello pro firmy
Aktuální metody ověřování s hesly samostatně nejsou dostatečná k chránit uživatele. Uživatelé opakovaně používat a zapomněli hesla. Hesla jsou breachable, phishable náchylné k praskliny a uhodnutelných. Také získat obtížné mějte na paměti a náchylné k útokům jako "[předat hodnotu hash](https://technet.microsoft.com/dn785092.aspx)".

## <a name="about-windows-hello-for-business"></a>O Windows Hello pro firmy
Windows Hello pro firmy je soukromého a veřejného klíče nebo ověřování pomocí certifikátů přístup pro organizace a spotřebitelé, která přesahuje hesla. Tato forma ověřování spoléhá na pár klíčů přihlašovací údaje, které může nahradit hesla a jsou odolné vůči narušení, krádeže a phishing.

 Windows Hello pro firmy umožňuje uživateli ověření účtu Microsoft, účet systému Windows Server Active Directory, účet Microsoft Azure Active Directory (Azure AD) nebo službu jiných společností než Microsoft, která podporuje ověřování rychlého IDentity Online (FIDO). Po počáteční dvoustupňové ověřování během Windows Hello pro firmy registraci Windows Hello pro firmy je nastavený na zařízení uživatele a uživatel nastaví gesto, což může být Windows Hello nebo PIN kód. Uživatel poskytuje gesto ověřit svou identitu. Windows pak používá Windows Hello pro firmy k ověření uživatele a pomoci jim přístup k chráněným prostředkům a službám.

Privátní klíč je k dispozici výhradně pomocí "gesto uživatele" jako PIN kód, biometrické nebo vzdálené zařízení, jako jsou čipové karty, které uživatel používá pro přihlášení k zařízení. Tyto informace se propojí certifikát nebo asymetrický dvojici klíčů. Privátní klíč je ověřeno, pokud má zařízení čipu Trusted Platform Module (TPM) hardwaru. Privátní klíč zařízení nikdy neopustí.

Veřejný klíč není registrována s Azure Active Directory a Windows Server Active Directory (pro místní). Zprostředkovatelé identity (IDPs) ověřit uživatele pomocí mapování veřejný klíč uživatele k privátnímu klíči a zadejte přihlašovací údaje prostřednictvím jednoho čas heslem (OTP), PhoneFactor nebo mechanismus různých oznámení.

## <a name="why-enterprises-should-adopt-windows-hello-for-business"></a>Proč podniky přijmout Windows Hello pro firmy
Když zapnete Windows Hello pro firmy, podniky můžete zabezpečit jejich prostředky i podle:

* Nastavení Windows Hello pro firmy s možností preferované hardwaru. To znamená, že se budou generovat klíče na čip TPM 1.2 nebo TPM 2.0, pokud je k dispozici. Když není TPM k dispozici, bude software generování klíče.
* Definování je jednodušší a má délku PIN kódu, a zda je povoleno Hello využití ve vaší organizaci.
* Konfigurace Windows Hello pro firmy pro podporu čipových karet jako scénáře pomocí vztahu důvěryhodnosti na základě certifikátů.

## <a name="how-windows-hello-for-business-works"></a>Jak Windows Hello pro firmy funguje
1. Klíče generované na hardwaru TPM nebo softwaru. Mnoho zařízení mají předdefinovaný čip TPM, který zabezpečuje hardware integrací kryptografické klíče do zařízení. Čip TPM 1.2 nebo TPM 2.0 generuje klíče ani certifikáty, které jsou vytvořené pomocí generovaného klíče.
2. Čip TPM osvědčuje tyto klíče vázané na hardwaru.
3. Gesto jeden odemčení odemkne zařízení. Tato gesto umožňuje přístup k více prostředkům Pokud zařízení není připojené k doméně nebo Azure AD připojený.

## <a name="how-the-windows-hello-for-business-lifecycle-works"></a>Windows Hello pro firmy životního cyklu fungování
![Windows Hello pro firmy životního cyklu](./media/active-directory-azureadjoin/active-directory-azureadjoin-microsoft-passport.png)

Předchozí diagram ukazuje soukromého a veřejného páru klíčů a ověřování pomocí zprostředkovatele identity. Každý z těchto kroků je podrobně popsány zde:

1. Uživatel prokáží jejich identitu prostřednictvím několik předdefinovaných kontroly pravopisu metod (gesta, fyzických čipových karet, služba Multi-Factor authentication) a odešle tyto informace do Identity zprostředkovatele (IDP) jako Azure Active Directory nebo místní služby Active Directory.
2. Zařízení vytvoří klíč, osvědčuje klíč, na server veřejnou část tohoto klíče přijímá, připojí s příkazy stanice, přihlášení a odešle ji do rozšíření IDP zaregistrujte klíč.
3. Jakmile rozšíření IDP zaregistruje na server veřejnou část klíče, vyzve rozšíření IDP zařízení se přihlásit s části privátního klíče.
4. Rozšíření IDP pak ověří a vydá token ověřování, která umožní přístup k zařízení a uživatele k chráněným prostředkům. IDPs můžete zapsat aplikací pro různé platformy nebo používat podporu prohlížeče (přes rozhraní API jazyka JavaScript nebo Webcrypto) Chcete-li vytvořit a používat Windows Hello pro firmy přihlašovací údaje pro své uživatele.

## <a name="the-deployment-requirements-for-windows-hello-for-business"></a>Požadavky na nasazení pro Windows Hello pro firmy
### <a name="at-the-enterprise-level"></a>Na úrovni organizace
* Podniku má předplatné Azure.

### <a name="at-the-user-level"></a>Na úrovni uživatele
* Počítači běží Windows 10 Professional nebo Enterprise.

Podrobné pokyny pro nasazení, najdete v části [povolit Windows Hello pro firmy v organizaci](active-directory-azureadjoin-passport-deployment.md).

## <a name="additional-information"></a>Další informace
* [Windows 10 pro firmy: Možnosti, jak používat zařízení pro práci](active-directory-azureadjoin-windows10-devices-overview.md)
* [Rozšíření možností cloudu u zařízení s Windows 10 prostřednictvím služby Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Další informace o scénářích použití pro službu Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Připojení zařízení k doméně služby Azure AD ve Windows 10 – ukázky z praxe](active-directory-azureadjoin-devices-group-policy.md)
* [Nastavení služby Azure AD Join](active-directory-azureadjoin-setup.md)

