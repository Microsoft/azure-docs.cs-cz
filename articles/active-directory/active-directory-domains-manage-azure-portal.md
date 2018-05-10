---
title: Správa vlastních názvů domén v Azure Active Directory | Microsoft Docs
description: Koncepty správy a postupy pro správu název domény v Azure Active Directory
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
ms.openlocfilehash: 81c2371d5dbb17399071c80ff4e8b81813ed014c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Správa vlastních názvů domén v Azure Active Directory
Název domény, je důležitou součástí identifikátoru pro mnoho prostředků adresáře: je součástí uživatelského jména nebo e-mailové adresy pro uživatele, část adresy pro skupinu a můžou být součástí identifikátor ID URI aplikace pro aplikaci. Prostředek v Azure Active Directory (Azure AD) může zahrnovat název domény, který už je ověřený jako vlastníkem adresáře, která obsahuje daný prostředek. Globální správce můžete provádět úlohy správy domény ve službě Azure AD.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Nastavte název primární domény pro váš adresář Azure AD
Při vytváření adresáře, název domény, jako je například 'contoso.onmicrosoft.com', je také název primární doménu. Primární doména je výchozí název domény pro nového uživatele při vytváření nového uživatele. Nastavení primární doménu zjednodušuje proces pro správce k vytváření nových uživatelů na portálu. Chcete-li změnit název primární domény:

1. Přihlaste se k [portál Azure](https://portal.azure.com) pomocí účtu, který je globální správce adresáře.
2. Vyberte **Azure Active Directory**.
3. Vyberte **vlastní názvy domén**.
     
   ![Správa uživatelů otevírání](./media/active-directory-domains-manage-azure-portal/add-custom-domain.png)
4. Vyberte název domény, který chcete být primární domény.
5. Vyberte **nastavit jako primární** příkaz. Potvrďte volbu po zobrazení výzvy.
   
   ![Zkontrolujte název domény, primární](./media/active-directory-domains-manage-azure-portal/make-primary-domain.png)

Můžete změnit název primární domény pro váš adresář na všechny ověřené vlastní doménu, která není federovaný. Změna primární domény pro váš adresář nedojde ke změně uživatelská jména pro všechny stávající uživatele.

## <a name="add-custom-domain-names-to-your-azure-ad-tenant"></a>Přidat vlastní názvy domén pro vašeho klienta Azure AD
Můžete přidat na maximálně 900 názvů spravované domény. Pokud konfigurujete vašich domén pro federaci s místní služby Active Directory, můžete přidat na maximálně 450 názvů domén v každý adresář. Další informace najdete v tématu [federovaný a názvy spravované domény](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain-concepts#federated-and-managed-domain-names).

## <a name="add-subdomains-of-a-custom-domain"></a>Přidat subdomény vlastní domény
Pokud chcete přidat název domény třetí úrovně například 'europe.contoso.com' do vašeho adresáře, měli byste nejprve přidat a ověřit domény druhé úrovně, například contoso.com. Subdoméně bude automaticky ověřit pomocí služby Azure AD. Pokud chcete zjistit, zda byla ověřena subdomény, kterou jste právě přidali, aktualizujte stránku v prohlížeči, který obsahuje seznam domén.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>Co dělat, když změníte registrátora DNS pro vlastní název domény
Pokud změníte registrátora DNS pro vlastní název domény, můžete nadále používat vlastní název domény se službou Azure AD samotné bez přerušení a bez další konfigurace úlohy. Pokud používáte vlastní název domény pro Office 365, Intune nebo jiné služby, které jsou závislé na vlastních názvů domén ve službě Azure AD, naleznete v dokumentaci pro tyto služby.

## <a name="delete-a-custom-domain-name"></a>Odstranit vlastní název domény
Vlastní název domény můžete odstranit ze služby Azure AD, pokud vaše organizace už používá název domény, nebo pokud budete muset použít název domény s jinou Azure AD.

Chcete-li odstranit vlastní název domény, musíte nejdřív zkontrolovat, že žádné prostředky ve vašem adresáři spoléhají na název domény. Název domény nelze odstranit z adresáře, pokud:

* Každý uživatel má uživatelské jméno, e-mailovou adresu nebo adresu proxy serveru, který zahrnuje název domény.
* Všechny skupiny má e-mailovou adresu nebo adresu proxy serveru, který zahrnuje název domény.
* Všechny aplikace ve službě Azure AD má aplikaci identifikátor ID URI, který zahrnuje název domény.

Musíte změnit nebo odstranit takových prostředků v adresáři služby Azure AD, chcete-li odstranit vlastní název domény.

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>Použijte PowerShell nebo rozhraní Graph API, které slouží ke správě názvů domén
Většinu úloh správy pro názvy domén v Azure Active Directory je možné dokončit také pomocí Microsoft PowerShell nebo programově pomocí rozhraní Azure AD Graph API.

* [Použití Powershellu ke správě názvů domén ve službě Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
* [Pomocí rozhraní Graph API ke správě názvů domén ve službě Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>Další postup
* [Přidání vlastních názvů domén](add-custom-domain.md)

