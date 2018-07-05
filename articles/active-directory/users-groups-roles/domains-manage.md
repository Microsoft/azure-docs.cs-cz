---
title: Správa vlastních názvů domén v Azure Active Directory | Dokumentace Microsoftu
description: Správa koncepty a postupy pro správu názvu domény ve službě Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 11/14/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.openlocfilehash: b503ef4c5cd922d4f7b58940cfc98a83a78ae986
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37449788"
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Správa vlastních názvů domén v Azure Active Directory
Název domény je důležitou součástí identifikátoru pro mnoho prostředků adresáře: je součástí uživatelského jména nebo e-mailové adresy pro uživatele, část adresy pro skupinu a můžou být součástí identifikátor URI ID aplikace pro aplikaci. Název domény, který už je ověřený jako vlastní adresáře, který obsahuje prostředek může obsahovat prostředků v Azure Active Directory (Azure AD). Jenom globální správce může provádět úkoly správy domény ve službě Azure AD.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Nastavit název domény primárního pro váš adresář Azure AD
Když se do adresáře, počáteční název domény, například "contoso.onmicrosoft.com", je také název primární doménu. Primární doména je výchozí název domény pro nového uživatele, když vytvoříte nového uživatele. Nastavení názvu domény primární zjednodušuje proces pro správce k vytváření nových uživatelů na portálu. Chcete-li změnit primární doména název:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) pomocí účtu, který má oprávnění globálního správce adresáře.
2. Vyberte **Azure Active Directory**.
3. Vyberte **vlastní názvy domén**.
     
   ![Otevírání správy uživatelů](./media/domains-manage/add-custom-domain.png)
4. Vyberte název domény, které mají být primární doménu.
5. Vyberte **nastavit jako primární** příkazu. Potvrďte svou volbu po zobrazení výzvy.
   
   ![Nastavit jako primární název domény](./media/domains-manage/make-primary-domain.png)

Můžete změnit název primární domény pro váš adresář bude ověřené vlastní doméně, která není Federovaná. Primární doména pro svůj adresář změna neovlivní uživatelských jmen pro všechny stávající uživatele.

## <a name="add-custom-domain-names-to-your-azure-ad-tenant"></a>Přidání vlastních názvů domén do svého tenanta Azure AD
Je můžete ušetřit až do maximálního počtu 900 názvů spravované domény. Pokud konfigurujete všech domén pro federaci s místní služby Active Directory, můžete přidat do maximálního počtu 450 názvů domén v každém adresáři. Další informace najdete v tématu [federovaných a spravovaných názvech domén](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain-concepts#federated-and-managed-domain-names).

## <a name="add-subdomains-of-a-custom-domain"></a>Přidání subdomén vlastní domény
Pokud chcete přidat název domény třetí úrovně jako je například 'europe.contoso.com' do adresáře, byste nejprve přidání a ověření domény druhé úrovně, jako je například contoso.com. Poddomény se automaticky ověří pomocí Azure AD. Pokud chcete zobrazit, že byla ověřena subdomény, kterou jste právě přidali, aktualizujte stránku v prohlížeči se zobrazí seznam domén.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>Co dělat, když změníte registrátora DNS pro název vaší vlastní domény
Pokud změníte registrátora DNS pro vlastní název domény, můžete nadále používat vlastní název domény s samotné služby Azure AD bez přerušení a bez dalších úloh konfigurace. Pokud používáte vlastní název domény s Office 365, Intune nebo jiných služeb, které spoléhají na vlastních názvů domén ve službě Azure AD, najdete v dokumentaci pro tyto služby.

## <a name="delete-a-custom-domain-name"></a>Odstranit vlastní název domény
Pokud vaše organizace už používá tento název domény nebo pokud budete muset použít tento název domény pomocí jiné služby Azure AD, můžete odstranit vlastní název domény z Azure AD.

Před odstranit vlastní název domény, musíte napřed zajistit, že žádné prostředky ve vašem adresáři Spolehněte se na název domény. Název domény nelze odstranit z adresáře, pokud:

* Každý uživatel má uživatelské jméno, e-mailová adresa nebo adresa proxy serveru, který zahrnuje název domény.
* Žádné skupiny má e-mailová adresa nebo adresa proxy serveru, který zahrnuje název domény.
* Všechny aplikace ve službě Azure AD má aplikace identifikátor URI pro ID, která zahrnuje název domény.

Musíte změnit nebo odstranit takových prostředků ve vašem adresáři Azure AD, než budete moct odstranit vlastní název domény.

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>Pomocí Powershellu nebo rozhraní Graph API ke správě názvů domén
Většina úkolů správy pro názvy domén v Azure Active Directory je možné provést i pomocí Powershellu Microsoft nebo programově pomocí rozhraní Azure AD Graph API.

* [Použití Powershellu ke správě názvů domén ve službě Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
* [Pomocí rozhraní Graph API ke správě názvů domén ve službě Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>Další postup
* [Přidání vlastních názvů domén](../fundamentals/add-custom-domain.md)

