---
title: Správa vašeho tenanta Azure Active Directory | Dokumentace Microsoftu
description: Další informace o jaké tenanta služby Azure AD je a jak spravovat Azure pomocí Azure Active Directory.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: lizross
ms.reviewer: jeffsta
custom: it-pro
ms.openlocfilehash: eb9cf7c21613ab91cc0b8e41a4454835d18f2c34
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46297924"
---
# <a name="manage-your-azure-active-directory-tenant"></a>Správa vašeho tenanta Azure Active Directory

## <a name="what-is-an-azure-ad-tenant"></a>Co je klient služby Azure AD?
V Azure Active Directory (Azure AD) je tenant vyhrazenou instancí služby Azure AD, kterou vaše organizace obdrží, když se přihlásí ke cloudové službě Microsoftu, jako je Azure nebo Office 365. Každý adresář služby Azure AD je oddělený od ostatních adresářů služby Azure AD. Adresář služby Azure AD byl navržen tak, aby se jednalo o zabezpečený prostředek k použití pouze pro vaši organizaci – stejně jako je podniková kancelářská budova zabezpečeným prostředkem výhradně vaší organizace. Architektura služby Azure AD izoluje data zákazníků a informace o identitě.To znamená, že se uživatelé a správci jednoho adresáře služby Azure AD nemohou dostat – ať už omylem nebo záměrně – k datům v jiném adresáři.

![Správa služby Azure Active Directory](./media/active-directory-administer/aad_portals.png)

## <a name="how-can-i-get-an-azure-ad-directory"></a>Jak získat adresář služby Azure AD?
Služba Azure AD poskytuje základní možnosti správy adresáře a identity pro většinu cloudových služeb Microsoftu, včetně těchto:

* Azure
* Microsoft Office 365
* Microsoft Dynamics CRM Online
* Microsoft Intune

Adresář Azure AD získáte při registraci libovolné z těchto cloudových služeb Microsoftu. Podle potřeby můžete vytvářet další adresáře. Například můžete první adresář používat jako produkční a vytvořit další adresář pro testování nebo přípravu.

### <a name="using-the-azure-ad-directory-that-comes-with-a-new-azure-subscription"></a>Použití adresáře služby Azure AD, který je součástí nového předplatného Azure

Při registraci dalších služeb společnosti Microsoft doporučujeme použít účet správce, který jste použili pro první službu. Informace, které zadáte při první registraci ke službě Microsoftu, se použijí k vytvoření nové instance adresáře Azure AD pro vaši organizaci. Pokud tento adresář použijete k ověřování pokusů o přihlášení, když se přihlašujete k odběru dalších služeb Microsoftu, můžete použít v stávající uživatelské účty, zásady, nastavení nebo místní integraci adresáře, které jste nakonfigurovali ve výchozím adresáři.

Pokud jste si například zaregistrovali předplatné Microsoft Intune a potom synchronizujete místní služby Active Directory s adresářem Azure AD, můžete si zaregistrovat další službu společnosti Microsoft, jako je třeba Office 365, a snadno dosáhnout stejných výhod integrace adresáře, které máte při využití Microsoft Intune.

Další informace o integraci místního adresáře se službou Azure AD najdete v článku o [integraci adresáře s Azure AD Connect](../hybrid/whatis-hybrid-identity.md).

### <a name="associate-an-existing-azure-ad-directory-with-a-new-azure-subscription"></a>Přidružení stávajícího adresáře služby Azure AD k novému předplatnému služby Azure
Nové předplatné služby Azure můžete přidružit ke stejnému adresáři, který používáte k ověřování přihlášení k existujícímu předplatnému služby Office 365 nebo Microsoft Intune. Další informace o tomto scénáři najdete v tématu [Přenos vlastnictví předplatného služby Azure na jiný účet](../../billing/billing-subscription-transfer.md).

### <a name="create-an-azure-ad-directory-by-signing-up-for-a-microsoft-cloud-service-as-an-organization"></a>Vytvoření adresáře služby Azure AD prostřednictvím registrace organizace ke cloudové službě Microsoftu
Pokud ještě nemáte předplatné cloudové služby Microsoftu, můžete k registraci využít některý z následujících odkazů. Při registraci k první službě se adresář služby Azure AD vytvoří automaticky.

* [Microsoft Azure](https://account.azure.com/organization)
* [Office 365](http://products.office.com/business/compare-office-365-for-business-plans/)
* [Microsoft Intune](https://portal.office.com/Signup/Signup.aspx?OfferId=40BE278A-DFD1-470a-9EF7-9F2596EA7FF9&dl=INTUNE_A&ali=1#0%20)

### <a name="how-to-change-the-default-directory-for-a-subscription"></a>Jak změnit výchozí adresář pro předplatné

1. Pokud chcete převést vlastnictví předplatného, přihlaste se k [Centru účtů Azure](https://account.azure.com/Subscriptions) pomocí účtu, který je správcem účtu pro příslušné předplatné.
2. Zkontrolujte, že uživatel, který se má stát vlastníkem předplatného, je v cílovém adresáři.
3. Klikněte na **Převod vlastnictví předplatného**.
4. Zadejte příjemce. Příjemce automaticky dostane e-mail s odkazem pro akceptaci.
5. Příjemce klikne na odkaz a postupuje podle pokynů, včetně zadání platebních údajů. Pokud je příjemce úspěšný, předplatné je převedeno. 
6. Výchozím adresářem předplatného se v případě úspěšného přenosu tohoto předplatného stane adresář, ve kterém je příslušný uživatel.

Další informace najdete v popisu [přenosu vlastnictví předplatného Azure na jiný účet](../../billing/billing-subscription-transfer.md).

### <a name="manage-the-default-directory-in-azure"></a>Správa výchozího adresáře v Azure
Při registraci v Azure se k vašemu předplatnému přidruží výchozí adresář Azure AD. Za používání služby Azure AD se neplatí a vaše adresáře jsou bezplatným prostředkem. Existují placené služby Azure AD, které se licencují samostatně a poskytují další funkce, jako je třeba firemní branding při přihlášení nebo samoobslužné resetování hesla. Můžete si také místo výchozí domény *.onmicrosoft.com vytvořit vlastní doménu s využitím názvu DNS, který vlastníte.

## <a name="how-can-i-manage-directory-data"></a>Jak spravovat data adresáře?
Jako správce jednoho nebo více předplatných cloudové služby Microsoftu můžete ke správě dat adresářů svých organizací použít [Centrum pro správu Azure AD](https://aad.portal.azure.com), Portál účtu Microsoft Intune nebo [Centrum pro správu Office 365](https://portal.office.com/). Můžete také použít [rutiny Modul Azure Active Directory PowerShellu](https://docs.microsoft.com/powershell/azure/active-directory), které vám pomohou spravovat data uložená ve službě Azure AD.

Pomocí kteréhokoli z těchto portálů (nebo rutin) můžete:

* vytvářet a spravovat účty uživatelů a skupin
* spravovat související cloudové služby pro předplatná vaší organizace
* nastavit místní integraci se službami ověřování a identit Azure AD

Centrum pro správu Azure AD, Centrum pro správu Office 365, Portál účtu Microsoft Intune i rutiny služby Azure AD čtou a zapisují v rámci jediné sdílené instance služby Azure AD, která je přidružená k adresáři vaší organizace. Každý z těchto nástrojů funguje jako front-endové rozhraní, které přijímá nebo mění data vašeho adresáře.
Pokud pomocí některého z portálů nebo rutin změníte data vaší organizace a jste v kontextu jedné z těchto služeb přihlášení, změny se při příštím přihlášení projeví také na ostatních portálech. Tato data se sdílejí napříč cloudovými službami Microsoftu, které odebíráte.

Pokud třeba v Centru pro správu Office 365 zablokujete určitému uživateli přihlašování, nemůže se tento uživatel přihlásit k žádné službě, kterou má vaše organizace předplacenou. Pokud se stejný uživatelský účet podíváte v Portálu účtu Microsoft Intune, také uvidíte, že tento uživatel je zablokovaný.

## <a name="how-can-i-add-and-manage-multiple-directories"></a>Jak přidat a spravovat více adresářů?
[Adresář Azure AD můžete přidat na webu Azure Portal](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory). Vyplňte informace a vyberte **Vytvořit**.

Každý adresář můžete spravovat jako plně nezávislý prostředek: každý adresář je rovnocenný, plně vybavený a logicky nezávislý na ostatních adresářích, které spravujete. Mezi adresáři neexistují žádné vztahy typu nadřazenost/podřazenost. Tato nezávislost mezi adresáři zahrnuje nezávislost prostředků, nezávislost správy a nezávislost synchronizace.

* **Nezávislost prostředků**. Vytvoření nebo odstranění prostředku v jednom adresáři nemá žádný vliv na prostředky v jiných adresářích. Částečnou výjimkou jsou externí uživatelé. Pokud používáte vlastní doménu „contoso.com“ u jednoho adresáře, nemůžete ji použít u jiných adresářů.
* **Nezávislost správy**.  Pokud uživatel adresáře Contoso, který nemá oprávnění správce, vytvoří testovací adresář Test:
  
  * Správci adresáře Contoso nemají žádná přímá oprávnění pro správu adresáře Test, pokud jim správce adresáře Test oprávnění explicitně neudělí. Správci adresáře Contoso mohou řídit přístup k adresáři Test na základě ovládání uživatelského účtu, který tento adresář Test vytvořil.
    
  * Pokud přiřadíte nebo odeberete roli správce u uživatele v jednom adresáři, neovlivní tato změna žádnou roli správce, kterou tento uživatel může mít v jiném adresáři.
* **Nezávislost synchronizace**. Jednotlivé tenanty Azure AD můžete nakonfigurovat nezávisle, a zajistit tak synchronizaci dat z jedné instance pomocí nástroje pro synchronizaci adresáře Azure AD Connect.

Na rozdíl od jiných prostředků služby Azure nejsou vaše adresáře podřízenými prostředky předplatného služby Azure. To znamená, že i v případě, že své předplatné služby Azure zrušíte nebo necháte vypršet, budete mít stále přístup k datům vašeho adresáře prostřednictvím rozhraní Azure AD PowerShell, Azure Graph API nebo dalších rozhraní, jako je například Centrum pro správu Office 365. K adresáři můžete také přidružit jiné předplatné.

## <a name="how-to-prepare-to-delete-an-azure-ad-directory"></a>Jak připravit odstranění adresáře služby Azure AD
Adresář služby Azure AD může prostřednictvím portálu odstranit globální správce. Při odstranění adresáře se také odstraní všechny prostředky, které jsou v tomto adresáři obsažené. Před odstraněním adresáře ověřte, že už ho nepotřebujete.

> [!NOTE]
> Pokud je uživatel přihlášený prostřednictvím pracovního nebo školního účtu, nesmí se pokoušet o odstranění vlastního domovského adresáře. Pokud je uživatel například přihlášený jako joe@contoso.onmicrosoft.com, nemůže odstranit adresář, který má jako výchozí doménu contoso.onmicrosoft.com.

Služba Azure AD vyžaduje, aby byly před odstraněním adresáře splněny určité podmínky. Tím se snižuje riziko, že odstranění adresáře negativně ovlivní uživatele nebo aplikace, například možnost uživatelů přihlásit se ke službě Office 365 nebo jejich přístup k prostředkům v Azure. Pokud by například někdo neúmyslně odstranil adresář pro předplatné, ztratili by uživatelé přístup k prostředkům služby Azure daného předplatného.

Kontrolují se následující podmínky:

* Globální správce, který se chystá adresář odstranit, by měl být jediným uživatelem v daném adresáři. Všichni ostatní uživatelé musí být před odstraněním adresáře odstraněni. Pokud uživatelé používají synchronizaci z místních adresářů, musí být tato synchronizace vypnutá a uživatele je potřeba z adresáře cloudu odstranit prostřednictvím webu Azure Portal nebo rutin Azure PowerShellu. Skupiny nebo kontakty, například kontakty přidané pomocí Centra pro správu Office 365, odstraňovat nemusíte.
* V adresáři nesmí být žádné aplikace. Všechny aplikace musí být před odstraněním adresáře odstraněny.
* S adresářem nesmí být propojení žádní poskytovatelé vícefaktorového ověřování.
* K adresáři nesmí být přidruženo žádné předplatné online služby Microsoft, jako jsou Microsoft Azure, Office 365 nebo Azure AD Premium. Například pokud byl váš výchozí adresář vytvořený v Azure, nemůžete ho odstranit, pokud stále slouží k ověřování přihlášení k předplatnému služby Azure. Podobně nelze odstranit adresář, pokud k němu má přidružené předplatné jiný uživatel. 


## <a name="next-steps"></a>Další postup
* [Fórum služby Azure AD](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=windowsazuread)
* [Fórum služby Azure Multi-Factor Authentication](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=windowsazureactiveauthentication)
* [StackOverflow pro dotazy ohledně Azure](https://stackoverflow.com/questions/tagged/azure-active-directory)
* [Azure Active Directory PowerShell](https://docs.microsoft.com/powershell/azure/active-directory)
* [Přiřazování rolí správce ve službě Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
