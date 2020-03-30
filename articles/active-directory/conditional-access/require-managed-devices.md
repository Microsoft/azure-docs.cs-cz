---
title: Podmíněný přístup vyžaduje spravované zařízení – Azure Active Directory
description: Zjistěte, jak nakonfigurovat zásady podmíněného přístupu založené na zařízení Azure Active Directory (Azure AD), které vyžadují spravovaná zařízení pro přístup ke cloudovým aplikacím.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/22/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a3c71534febc3cdb6429d3092225ebc73f6cbe7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481479"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>Postup: Vyžadovat spravovaná zařízení pro přístup ke cloudovým aplikacím pomocí podmíněného přístupu

Azure Active Directory (Azure AD) umožňuje jednotné přihlašování k aplikacím a službám odkudkoli v cloudovém světě. Oprávnění uživatelé mohou přistupovat k vašim cloudovým aplikacím z široké škály zařízení, včetně mobilních i osobních zařízení. Mnoho prostředí však má alespoň několik aplikací, ke kterým by měly přistupovat pouze zařízení, která splňují vaše standardy zabezpečení a dodržování předpisů. Tato zařízení se také označují jako spravovaná zařízení. 

Tento článek vysvětluje, jak můžete nakonfigurovat zásady podmíněného přístupu, které vyžadují spravovaná zařízení pro přístup k určitým cloudovým aplikacím ve vašem prostředí. 

## <a name="prerequisites"></a>Požadavky

Vyžadování spravovaných zařízení pro přístup ke cloudovým aplikacím spojuje **podmíněný přístup Azure AD** a **správu zařízení Azure AD** dohromady. Pokud ještě nejste obeznámeni s některou z těchto oblastí, měli byste si nejprve přečíst následující témata:

- **[Podmíněný přístup ve službě Azure Active Directory](../active-directory-conditional-access-azure-portal.md)** – tento článek poskytuje koncepční přehled podmíněného přístupu a související terminologie.
- **[Úvod ke správě zařízení ve službě Azure Active Directory](../devices/overview.md)** – tento článek poskytuje přehled různých možností, které potřebujete k získání zařízení pod organizační kontrolou. 
- Pro podporu Chromu v **aktualizaci Windows 10 Creators Update (verze 1703)** nebo novější nainstalujte [rozšíření O účty s Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). Toto rozšíření je vyžadováno, pokud zásady podmíněného přístupu vyžadují podrobnosti specifické pro zařízení.

>[!NOTE] 
> Doporučujeme používat zásady podmíněného přístupu založené na zařízení Azure AD, abyste získali nejlepší vynucení po počátečním ověřování zařízení. To zahrnuje uzávěrky relací, pokud zařízení vypadne z dodržování předpisů a tok kódu zařízení.


## <a name="scenario-description"></a>Popis scénáře

Zvládnutí rovnováhy mezi bezpečností a produktivitou je výzvou. Rozšíření podporovaných zařízení pro přístup ke cloudovým prostředkům pomáhá zvýšit produktivitu uživatelů. Na druhou stranu pravděpodobně nechcete, aby určité prostředky ve vašem prostředí byly přístupné zařízením s neznámou úrovní ochrany. U ohrožených prostředků byste měli vyžadovat, aby k nim uživatelé měli přístup pouze pomocí spravovaného zařízení. 

S podmíněným přístupem Azure AD můžete tento požadavek řešit pomocí jediné zásady, která uděluje přístup:

- K vybraným cloudovým aplikacím
- Pro vybrané uživatele a skupiny
- Vyžadování spravovaného zařízení

## <a name="managed-devices"></a>Spravovaná zařízení  

Jednoduše řečeno, spravovaná zařízení jsou zařízení, která jsou pod *nějakou* organizační kontrolou. Ve službě Azure AD je předpokladem spravovaného zařízení, že je registrované ve službě Azure AD. Registrace zařízení vytvoří identitu pro zařízení ve formě objektu zařízení. Tento objekt používá Azure ke sledování informací o stavu zařízení. Jako správce Azure AD, můžete již použít tento objekt přepnout (povolit nebo zakázat) stav zařízení.
  
![Podmínky založené na zařízení](./media/require-managed-devices/32.png)

Chcete-li získat zařízení registrované ve službě Azure AD, máte tři možnosti: 

- **Registrovaná zařízení Azure AD** – získání osobního zařízení registrovaného ve službě Azure AD
- **Azure AD připojenzařízení** – získat organizační zařízení s Windows 10, které není připojen k místní ad registrované s Azure AD. 
- **Hybridní Azure AD připojenzařízení** – chcete-li získat Windows 10 nebo podporované zařízení nižší úrovně, které je připojeno k místní ad registrované ve službě Azure AD.

Tyto tři možnosti jsou popsány v článku [Co je identita zařízení?](../devices/overview.md)

Chcete-li se stát spravovaným zařízením, musí být registrované zařízení zařízení **připojeno k hybridnímu azure ad** nebo **zařízení, které bylo označeno jako kompatibilní**.  

![Podmínky založené na zařízení](./media/require-managed-devices/47.png)
 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Vyžadovat hybridní zařízení připojená k Azure AD

V zásadách podmíněného přístupu můžete vybrat **vyžadovat hybridní zařízení spojené s Azure AD,** abyste mohli uvést, že vybrané cloudové aplikace jsou přístupné jenom pomocí spravovaného zařízení. 

![Podmínky založené na zařízení](./media/require-managed-devices/10.png)

Toto nastavení platí jenom pro windows 10 nebo zařízení nižší úrovně, jako je Windows 7 nebo Windows 8, která jsou připojená k místnímu službě AD. Tato zařízení můžete zaregistrovat jenom pomocí Azure AD pomocí hybridního připojení Azure AD, což je [automatizovaný proces](../devices/hybrid-azuread-join-plan.md) k registraci zařízení s Windows 10. 

![Podmínky založené na zařízení](./media/require-managed-devices/45.png)

Proč se hybridní Azure AD připojil zařízení spravované zařízení?  U zařízení, která jsou připojena k místní službě AD, se předpokládá, že kontrola nad těmito zařízeními je vynucena pomocí řešení pro správu, jako je **configuration manager** nebo **zásady skupiny (GP)** pro jejich správu. Vzhledem k tomu, že neexistuje žádná metoda pro Azure AD k určení, zda některá z těchto metod byla použita na zařízení, vyžadující hybridní zařízení spojené s Azure AD je relativně slabý mechanismus vyžadovat spravované zařízení. Je na vás jako na správci, abyste posoudili, jestli metody, které se používají pro vaše místní zařízení připojená k doméně, jsou dostatečně silné, abyste představovali spravované zařízení, pokud je takové zařízení také zařízení s hybridním připojením Azure AD.

## <a name="require-device-to-be-marked-as-compliant"></a>Vyžadovat, aby zařízení bylo označeno jako vyhovující

Možnost *vyžadovat, aby zařízení bylo označeno jako vyhovující,* je nejsilnější masovou formou, která požaduje spravované zařízení.

![Podmínky založené na zařízení](./media/require-managed-devices/11.png)

Tato možnost vyžaduje, aby se zařízení zaregistrovalo ve službě Azure AD a také bylo označeno jako kompatibilní:
         
- Intune
- Systém správy mobilních zařízení (MDM) od jiného výrobce, který spravuje zařízení s Windows 10 prostřednictvím integrace Azure AD. Systémy MDM jiných výrobců pro jiné typy operačních systémů zařízení než Windows 10 nejsou podporovány.
 
![Podmínky založené na zařízení](./media/require-managed-devices/46.png)

U zařízení, které je označeno jako vyhovující, můžete předpokládat, že: 

- Mobilní zařízení, která vaši zaměstnanci používají pro přístup k firemním datům, jsou spravována
- Mobilní aplikace, které vaši zaměstnanci používají, jsou spravované
- Informace o vaší společnosti jsou chráněny tím, že pomáhají kontrolovat způsob, jakým vaši zaměstnanci přistupují a sdílejí je
- Zařízení a jeho aplikace jsou v souladu s požadavky na zabezpečení společnosti

### <a name="known-behavior"></a>Známé chování

Ve Windows 7, iOS, Android, macOS a některé webové prohlížeče třetích stran Azure AD identifikuje zařízení pomocí klientského certifikátu, který se zřídí, když je zařízení registrované ve službě Azure AD. Při prvním přihlášení uživatele prostřednictvím prohlížeče je uživatel vyzván k výběru certifikátu. Koncový uživatel musí vybrat tento certifikát, aby mohl pokračovat v používání prohlížeče.

## <a name="next-steps"></a>Další kroky

Než nakonfigurujete zásady podmíněného přístupu založenéna zařízení ve vašem prostředí, měli byste se podívat na [osvědčené postupy pro podmíněný přístup ve službě Azure Active Directory](best-practices.md).
