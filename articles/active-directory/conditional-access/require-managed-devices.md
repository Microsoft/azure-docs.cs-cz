---
title: Podmíněný přístup vyžaduje spravované zařízení – Azure Active Directory
description: Naučte se konfigurovat zásady podmíněného přístupu na základě zařízení Azure Active Directory (Azure AD), které vyžadují spravovaná zařízení pro cloudovou aplikaci přístup.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: c11b58954eefda67f981d618b04ab2bd69fa6b43
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93077757"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>Postupy: vyžadování spravovaných zařízení pro přístup k cloudovým aplikacím pomocí podmíněného přístupu

V cloudu, který je mobilní a celosvětový, Azure Active Directory (Azure AD) umožňuje jednotné přihlašování k aplikacím a službám odkudkoli. Autorizovaní uživatelé mají přístup k vašim cloudovým aplikacím z široké škály zařízení, včetně mobilních a i osobních zařízení. Mnoho prostředí však má alespoň několik aplikací, které by měly být k dispozici pouze pro zařízení, která splňují vaše standardy zabezpečení a dodržování předpisů. Tato zařízení se také označují jako spravovaná zařízení. 

Tento článek vysvětluje, jak můžete nakonfigurovat zásady podmíněného přístupu, které vyžadují spravovaná zařízení pro přístup k určitým cloudovým aplikacím ve vašem prostředí. 

## <a name="prerequisites"></a>Předpoklady

Vyžadování spravovaných zařízení pro cloudový přístup k Azure AD je spojeno s **podmíněným přístupem** a **správou zařízení Azure AD** . Pokud jste ještě neseznámili s jednou z těchto oblastí, měli byste si nejdřív přečíst následující témata:

- **[Podmíněný přístup v Azure Active Directory](./overview.md)** – Tento článek poskytuje koncepční přehled podmíněného přístupu a související terminologie.
- **[Seznámení se správou zařízení v Azure Active Directory](../devices/overview.md)** – Tento článek poskytuje přehled různých možností, které je třeba získat v rámci organizačního řízení. 
- V případě podpory pro Chrome ve **Windows 10 Creators Update (verze 1703)** nebo novější nainstalujte [rozšíření účtů Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). Toto rozšíření se vyžaduje, když zásada podmíněného přístupu vyžaduje podrobnosti konkrétního zařízení.

>[!NOTE] 
> Doporučujeme použít zásady podmíněného přístupu na základě zařízení Azure AD, abyste získali nejlepší vynucování po počátečním ověření zařízení. Patří sem uzavírací relace, pokud zařízení nedodržuje předpisy a tok kódu zařízení.


## <a name="scenario-description"></a>Popis scénáře

Hlavní vyvážení mezi zabezpečením a produktivitou je výzvou. Rozšíření podporovaných zařízení pro přístup k vašim cloudovým prostředkům pomáhá zlepšit produktivitu vašich uživatelů. Na překlopení nebudete pravděpodobně chtít, aby zařízení s neznámou úrovní ochrany měla k některým prostředkům ve vašem prostředí. U ovlivněných prostředků byste měli vyžadovat, aby k nim uživatelé měli přístup jenom pomocí spravovaného zařízení. 

Pomocí podmíněného přístupu Azure AD můžete tento požadavek vyřešit pomocí jediné zásady, která udělí přístup:

- Pro vybrané cloudové aplikace
- Pro vybrané uživatele a skupiny
- Vyžadování spravovaného zařízení

## <a name="managed-devices"></a>Spravovaná zařízení  

V jednoduchých výrazech jsou spravovaná zařízení zařízení, která jsou v rámci *určitého uspořádání* organizačního řízení. V Azure AD je předpokladem pro spravované zařízení, že je zaregistrovaný ve službě Azure AD. Registrace zařízení vytvoří identitu pro zařízení ve formě objektu zařízení. Tento objekt používá Azure ke sledování informací o stavu zařízení. Jako správce Azure AD už můžete tento objekt použít k přepínání (povolení nebo zakázání) stavu zařízení.
  
:::image type="content" source="./media/require-managed-devices/32.png" alt-text="Snímek obrazovky s podoknem zařízení v Azure A D. je zvýrazněna možnost povolit a zakázat položky." border="false":::

Pokud chcete získat zařízení zaregistrované ve službě Azure AD, máte tři možnosti: 

- **Zařízení registrovaná v Azure AD** – k získání osobního zařízení zaregistrovaného ve službě Azure AD
- **Zařízení připojená k Azure AD** – k získání firemního zařízení s Windows 10, které není připojené k místní službě AD zaregistrovanou ve službě Azure AD. 
- **Hybridní zařízení připojená k Azure AD** – Pokud chcete získat Windows 10 nebo podporovaná zařízení nižší úrovně, která jsou připojená k místní službě AD zaregistrovanou ve službě Azure AD.

Tyto tři možnosti jsou popsány v článku [co je identita zařízení?](../devices/overview.md)

Aby se stala spravované zařízení, musí se jednat o zařízení připojené k **hybridní službě Azure AD** nebo o **zařízení, které je označené jako vyhovující** .  

:::image type="content" source="./media/require-managed-devices/47.png" alt-text="Snímek obrazovky s podoknem zařízení v Azure A D. je zvýrazněna možnost povolit a zakázat položky." border="false":::
 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Vyžadovat zařízení připojená k hybridní službě Azure AD

V rámci zásad podmíněného přístupu můžete vybrat **vyžadovat zařízení připojené k hybridní službě Azure AD** , ke kterému se mají přístup k vybraným cloudovým aplikacím přistupovat jenom pomocí spravovaného zařízení. 

:::image type="content" source="./media/require-managed-devices/10.png" alt-text="Snímek obrazovky s podoknem zařízení v Azure A D. je zvýrazněna možnost povolit a zakázat položky." border="false":::

Toto nastavení platí jenom pro zařízení s Windows 10 nebo nižší úrovně, jako je Windows 7 nebo Windows 8, která jsou připojená k místní službě AD. Tato zařízení můžete zaregistrovat jenom ve službě Azure AD s využitím hybridního připojení k Azure AD, což je [automatizovaný proces](../devices/hybrid-azuread-join-plan.md) získání registrovaného zařízení s Windows 10. 

:::image type="content" source="./media/require-managed-devices/45.png" alt-text="Snímek obrazovky s podoknem zařízení v Azure A D. je zvýrazněna možnost povolit a zakázat položky." border="false":::

K čemu zařízení připojené k hybridní službě Azure AD využívá spravované zařízení?  U zařízení, která jsou připojená k místní službě AD, se předpokládá, že se ovládací prvek u těchto zařízení vynutil pomocí řešení pro správu, jako je **Configuration Manager** nebo **Zásady skupiny (GP)** , abyste je mohli spravovat. Vzhledem k tomu, že neexistuje žádná metoda pro Azure AD, která by mohla zjistit, jestli se některá z těchto metod v zařízení nepoužívala, je nutné, aby zařízení připojené k hybridní službě Azure AD mělo poměrně slabý mechanismus pro vyžadování spravovaného zařízení. Je to na vás jako správce, aby bylo možné posoudit, jestli jsou metody, které se vztahují na vaše místní zařízení připojená k doméně, dostatečně silné, aby představovaly spravované zařízení, pokud takové zařízení je také hybridní zařízení připojené k Azure AD.

## <a name="require-device-to-be-marked-as-compliant"></a>Vyžadovat, aby zařízení bylo označené jako vyhovující

Možnost vyžadovat, *aby zařízení byla označena jako vyhovující předpisům* , je nejsilnějším formulářem pro vyžádání spravovaného zařízení.

:::image type="content" source="./media/require-managed-devices/11.png" alt-text="Snímek obrazovky s podoknem zařízení v Azure A D. je zvýrazněna možnost povolit a zakázat položky." border="false":::

Tato možnost vyžaduje, aby zařízení bylo zaregistrované ve službě Azure AD a taky bylo označeno jako vyhovující tomuto:
         
- Intune
- Systém správy mobilních zařízení (MDM) třetí strany, který spravuje zařízení s Windows 10 prostřednictvím integrace služby Azure AD. Systémy MDM třetích stran pro jiné typy operačních systémů zařízení, než je Windows 10, se nepodporují.
 
:::image type="content" source="./media/require-managed-devices/46.png" alt-text="Snímek obrazovky s podoknem zařízení v Azure A D. je zvýrazněna možnost povolit a zakázat položky." border="false":::

U zařízení, které je označeno jako vyhovující, můžete předpokládat, že: 

- Mobilní zařízení, která vaši zaměstnanci používají pro přístup k firemním datům, se spravují.
- Mobilní aplikace, které vaše zaměstnanci používají, se spravují.
- Vaše firemní informace jsou chráněné tím, že pomáhají řídit způsob, jakým vaše zaměstnanci přistupují a sdílí je.
- Zařízení a jeho aplikace vyhovují požadavkům na zabezpečení společnosti.

### <a name="scenario-require-device-enrollment-for-ios-and-android-devices"></a>Scénář: vyžadování registrace zařízení pro zařízení s iOS a Androidem

V tomto scénáři společnost Contoso rozhodla, že veškerý mobilní přístup k prostředkům Microsoft 365 musí používat zaregistrované zařízení. Všichni uživatelé už přihlásili pomocí přihlašovacích údajů Azure AD a mají přiřazené licence, které zahrnují Azure AD Premium P1 nebo P2 a Microsoft Intune.

Organizace musí provést následující kroky, aby vyžadovaly použití zaregistrovaného mobilního zařízení.

1. Přihlaste se k **Azure Portal** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Vyhledejte **Azure Active Directory**  >  **Security**  >  **podmíněný přístup** zabezpečení.
1. Vyberte **nové zásady** .
1. Zadejte název zásady. Pro názvy svých zásad doporučujeme organizacím vytvořit smysluplný Standard.
1. V části **přiřazení** vyberte **Uživatelé a skupiny** .
   1. V části **Zahrnout** vyberte **všechny uživatele** nebo konkrétní **uživatele a skupiny** , u kterých chcete použít tuto zásadu. 
   1. Vyberte **Hotovo** .
1. V části **cloudové aplikace nebo akce**  >  **Include** vyberte **Office 365** .
1. V části **podmínky** vyberte **platformy zařízení** .
   1. Nastavte **Konfigurovat** na **Ano** .
   1. Zahrňte **Android** a **iOS** .
1. V části **řízení přístupu**  >  **udělení** přístupu vyberte následující možnosti:
   - **Vyžadovat, aby zařízení bylo označené jako vyhovující**
1. Potvrďte nastavení a nastavte **možnost povolit zásadu** na **zapnuto** .
1. Vyberte **vytvořit** a vytvořte a povolte zásady.

### <a name="known-behavior"></a>Známé chování

Při použití [toku OAuth pro kód zařízení](../develop/v2-oauth2-device-code.md)se nepodporuje stav vyžadovat řízení udělení spravovaného zařízení nebo stav zařízení. Důvodem je to, že zařízení, které provádí ověřování, nemůže poskytnout stav zařízení zařízení, které poskytuje kód, a stav zařízení je v tokenu zamčený k zařízení, které provádí ověřování. Místo toho použijte ovládací prvek vyžadovat službu Multi-Factor Authentication.

Ve Windows 7, iOS, Androidu, macOS a některých webových prohlížečích třetích stran služba Azure AD identifikuje zařízení pomocí klientského certifikátu, který se zřídí při registraci zařízení ve službě Azure AD. Když se uživatel poprvé přihlásí prostřednictvím prohlížeče, zobrazí se uživateli výzva k výběru certifikátu. Koncový uživatel musí tento certifikát vybrat předtím, než bude moci pokračovat v používání prohlížeče.

## <a name="next-steps"></a>Další kroky

[Vyhodnoťte dopad zásad podmíněného přístupu předtím, než povolíte rozsáhlou práci s režimem pouze sestavy](concept-conditional-access-report-only.md).
