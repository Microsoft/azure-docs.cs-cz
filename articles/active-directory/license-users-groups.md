---
title: Licence uživatelům v Azure Active Directory | Microsoft Docs
description: Naučte se licence vy a vaši uživatelé v Azure Active Directory.
services: active-directory
documentationcenter: ''
author: jeffgilb
manager: mtillman
editor: ''
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: jeffgilb
custom: it-pro
ms.openlocfilehash: 7dc71b33f542cf49d8a0abdb16e6e2f21e5dc2a0
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34713741"
---
# <a name="quickstart-license-users-in-azure-active-directory"></a>Rychlý úvod: Licence uživatelům v Azure Active Directory
Azure AD na základě licenční služby pracovní aktivací předplatné služby Azure Active Directory (Azure AD) ve vašem klientu Azure. Po předplatné je aktivní, jsou možnosti služby spravují správci nástroje Azure AD a používá licencovaní uživatelé. Při nákupu Enterprise Mobility + Security, Azure AD Premium nebo Azure AD Basic vašeho klienta je aktualizován předplatného, včetně jeho platnosti a předplacenou licence. Informace o vašem předplatném, včetně počtu licencí, přiřazené nebo k dispozici, je k dispozici prostřednictvím portálu Azure v části **Azure Active Directory** otevřením **licence** dlaždici. **Licence** okno je také nejlepší místo k spravovat vaše přiřazení licence.

I když získání předplatného je vše, budete muset nakonfigurovat placené funkce, musí stále přiřadit uživatelské licence pro placené služby Azure AD placené funkce. Každý uživatel, který mají mít přístup k nebo který je spravován prostřednictvím Azure AD placené funkce musí být přiřazena licence. Přiřazení licence je mapování mezi uživateli a zakoupené služby, jako je například Azure AD Premium, Basic nebo Enterprise Mobility + Security.

Můžete použít [přiřazení na základě skupiny licencí](active-directory-licensing-whatis-azure-portal.md) pravidel například následující:
* Všichni uživatelé v adresáři automaticky získat licenci
* Všichni uživatelé s názvem příslušné úlohy využít licenci
* Můžete delegovat rozhodnutí jiné správce v organizaci (pomocí [samoobslužných skupin](active-directory-accessmanagement-self-service-group-management.md))

> [!TIP]
> Podrobné informace o přiřazení licence na skupiny, včetně pokročilé scénáře a Office 365 licencování scénáře, najdete v části [přiřadit licence pro uživatele na základě členství ve skupině v Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md).

## <a name="assign-licenses-to-users-and-groups"></a>Přiřazení licencí uživatelům a skupinám
Pomocí aktivní předplatné, musí nejdřív přiřadit licenci k sami a aktualizujte webový prohlížeč k zajištění, že se zobrazí všechny očekávané funkce, které jsou součástí vašeho předplatného. Dalším krokem je přiřadit licence uživatelům, kteří potřebují přístup k placené funkce Azure AD. Snadný způsob, jak přiřadit licence je přiřadit licence na skupiny uživatelů a nikoli pro jednotlivce. Přiřadíte-li do skupiny licencí, jsou všechny členy skupiny přiřazenou licenci. Je-li přidat nebo odebrat ze skupiny uživatelů, příslušné licence automaticky přiřadit nebo odebrat. 

> [!NOTE]
> Některé služby společnosti Microsoft nejsou k dispozici ve všech umístěních. Předtím, než je možné přiřadit licence pro uživatele, Správce musí určit **umístění využití** vlastnost pro uživatele. Můžete nastavit tuto vlastnost pod **uživatele** &gt; **profil** &gt; **nastavení** na portálu Azure. Při použití přiřazení skupiny licencí, zdědí všechny uživatele, jejichž umístění využití nezadáte umístění adresáře.

Chcete-li přiřadit licenci, v části **Azure Active Directory** &gt; **licence** &gt; **všechny produkty, které**, vyberte jeden nebo více produktů a pak vyberte **přiřadit** na panelu příkazů.

![Vyberte licence přiřadit](media/license-users-groups/select-license-to-assign.png)

Můžete použít **uživatelů a skupin** okno vybrat více uživatelů nebo skupin nebo zakázat plány služeb v rámci produktu. Pomocí vyhledávacího pole v horní části vyhledat názvy uživatelů a skupin.

![Vyberte uživatele nebo skupiny pro přiřazení licence](media/license-users-groups/select-user-for-license-assignment.png)

Přiřadíte-li do skupiny licencí, může trvat nějakou dobu, než všichni uživatelé dědění licence, které jsou v závislosti na velikosti skupiny. Stav zpracování můžete zkontrolovat na **skupiny** okno, v části **licence** dlaždici.

![Stav přiřazení licencí](media/license-users-groups/license-assignment-status.png)

Přiřazení chyby může dojít během přiřazení licence Azure AD, ale jsou relativně vzácné, při správě Azure AD a Enterprise Mobility + Security produkty. Potenciální chyby při přiřazení jsou omezeny na:
- Přiřazení konflikt: když uživatel byl dříve přiřazen licenci, která není kompatibilní s aktuální licencí. V takovém případě přiřazování novou licenci vyžaduje odeberete aktuální.
- Překročil dostupné licence: Pokud počet uživatelů v přiřazených skupinách překročí dostupné licence, stav přiřazení uživatele, odráží selhání přiřadit z důvodu chybějící licence.

### <a name="azure-ad-b2b-collaboration-licensing"></a>Licencování Azure AD s B2B spolupráce

Spolupráce B2B můžete pozvat uživatele typu Host do vašeho klienta Azure AD k poskytování přístupu ke službám Azure AD a veškeré prostředky Azure dáte k dispozici.  

Není nijak zpoplatněn pozvat B2B uživatele a přiřadit je k aplikaci ve službě Azure AD. Až 10 aplikací na uživatele guest a 3 základních sestav jsou také zdarma pro uživatele spolupráce B2B. Pokud vaše uživatele guest má přiřazené v klientovi Azure AD jeho všechny odpovídající licence, že budete mít licenci v váš také.

Není to nutné, ale pokud chcete poskytnout přístup k placené funkce Azure AD, musí mít licenci tyto uživatele typu Host B2B s příslušným licence Azure AD. Klient služby pozváním s placenou licenci Azure AD můžete přiřadit spolupráce B2B uživatelská práva dalším uživatelům pět hosta pozvánku, abyste klienta. Scénáře a informace najdete v tématu [spolupráce B2B licencování pokyny](active-directory-b2b-licensing.md).

## <a name="view-assigned-licenses"></a>Zobrazit přiřazené licence

Souhrnné zobrazení přiřazené a k dispozici licence se zobrazí v části **Azure Active Directory** &gt; **licence** &gt; **všechny produkty**.

![Zobrazení souhrnu licencí](media/license-users-groups/view-license-summary.png)

Podrobný seznam přiřazené uživatelů a skupin je k dispozici, když vyberete určitý produkt. **Licencovaní uživatelé** seznam obsahuje všechny aktuálně spotřebě licenci a jestli byl přiřazen licence, které jsou přímo na uživatele nebo pokud je zděděno od skupiny uživatelů.

![Zobrazit podrobnosti o licenci](media/license-users-groups/view-license-detail.png)

Podobně **licenci skupiny** seznamu jsou uvedeny všechny skupiny, ke kterým byla přiřazena licence. Vyberte uživatele nebo skupiny otevřete **licence** okno, které znázorňuje všechny licence se přiřadila tento objekt.

## <a name="remove-a-license"></a>Odebrání licence

Chcete-li odebrat licenci, přejděte na uživatele nebo skupinu a otevřete **licence** dlaždici. Vyberte licenci a klikněte na tlačítko **odebrat**.

![Odebrání licence](media/license-users-groups/remove-license.png)

Licence zdědí uživatele ze skupiny nelze odebrat přímo. Místo toho odeberte uživatele ze skupiny, ze kterého se jsou dědění licence.


## <a name="next-steps"></a>Další postup
V tento rychlý start když jste se naučili přiřazení licencí uživatelům a skupinám v adresář Azure AD. 

Na následující odkaz můžete použít ke konfiguraci přiřazení licencí předplatného v Azure AD na portálu Azure.

> [!div class="nextstepaction"]
> [Přiřazení licencí Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Overview) 