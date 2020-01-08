---
title: Vytváření pracovních prostorů pro portály moje aplikace v Azure Active Directory | Microsoft Docs
description: Pracovní prostory moje aplikace můžete použít k přizpůsobení stránek Moje aplikace pro jednodušší prostředí aplikací pro koncové uživatele. Uspořádejte aplikace do skupin pomocí samostatných karet.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2019
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: e8e1fd51e0190e0f8889112b17b58680ed9329e3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443452"
---
# <a name="create-workspaces-on-the-my-apps-preview-portal"></a>Vytváření pracovních prostorů na portálu moje aplikace (Preview)

Uživatelé můžou pomocí portálu moje aplikace (Preview) zobrazovat a spouštět cloudové aplikace, ke kterým mají přístup. Ve výchozím nastavení jsou všechny aplikace, ke kterým má uživatel přístup, uvedené na jedné stránce. Pro lepší uspořádání této stránky pro uživatele, pokud máte licenci Azure AD Premium P1 nebo P2, můžete nastavit pracovní prostory. V pracovním prostoru můžete seskupit aplikace, které souvisejí (například podle role úlohy, úkolu nebo projektu) a zobrazit je na samostatné kartě. Pracovní prostor v podstatě používá filtr pro aplikace, které uživatel už může mít přístup, takže uživatel uvidí jenom aplikace v pracovním prostoru, které jim byly přiřazeny.

> [!NOTE]
> Tento článek popisuje, jak může správce povolit a vytvořit pracovní prostory. Informace o tom, jak používat portál a pracovní prostory moje aplikace, najdete v tématu věnovaném [přístupu a používání pracovních prostorů](https://docs.microsoft.com/azure/active-directory/user-help/my-applications-portal-workspaces).

## <a name="enable-my-apps-preview-features"></a>Povolit funkce verze Preview pro moje aplikace

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako správce uživatele nebo globální správce.

2. Přejít na **Azure Active Directory** > **nastavení uživatele**.

3. V části **náhledy uživatelských funkcí**vyberte **Spravovat nastavení uživatelské funkce Náhled**.

4. V části **Uživatelé můžou používat funkce verze Preview pro moje aplikace**, vyberte jednu z následujících možností:
   * **Vybráno** – povolí funkce ve verzi Preview pro konkrétní skupinu. Pomocí možnosti **Vybrat skupinu** vyberte skupinu, pro kterou chcete povolit funkce ve verzi Preview.  
   * **All** – povolí funkce ve verzi Preview pro všechny uživatele.

   ![Funkce ve verzi Preview uživatele](media/access-panel-workspaces/user-preview-features.png)

> [!NOTE]
> K otevření portálu moje aplikace můžou uživatelé použít odkaz `https://myapps.microsoft.com` nebo vlastní odkaz pro vaši organizaci, jako je například `https://myapps.microsoft.com/contoso.com`. Po povolení prostředí nové moje aplikace se v horní části stránky Moje aplikace zobrazí nápis **aktualizované moje prostředí moje aplikace** a uživatelé si **můžou vybrat, že si budou** moct zobrazit nové prostředí. Pokud chcete přestat používat nové prostředí, uživatelé můžou v horní části stránky vybrat **Ano** v nápisu **opustit nové prostředí** .

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

Pokud chcete vytvořit pracovní prostor, musíte mít licenci Azure AD Premium P1 nebo P2.

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako správce s licencí Azure AD Premium P1 nebo P2.

2. Přejít na **Azure Active Directory** > **podnikových aplikací**.

3. V části **Spravovat**vyberte **pracovní prostory (Preview)** .

4. Vyberte **nový pracovní prostor**. Na stránce **nový pracovní prostor** zadejte **název** pracovního prostoru (v názvu nedoporučujeme používat "pracovní prostor". Pak zadejte **Popis**.

   ![Vytvořit nový pracovní prostor](media/access-panel-workspaces/new-workspace.png)

5. Vyberte **zkontrolovat + vytvořit**. Zobrazí se vlastnosti nového pracovního prostoru.

6. Vyberte kartu **aplikace** . V části **Přidat aplikace**vyberte všechny aplikace, které chcete přidat do pracovního prostoru, nebo pomocí **vyhledávacího** pole vyhledejte aplikace. 

   ![Přidat do pracovního prostoru aplikace](media/access-panel-workspaces/add-applications.png)

7. Vyberte **Přidat**. Zobrazí se seznam vybraných aplikací. Pomocí šipek nahoru a dolů můžete změnit pořadí aplikací v seznamu.

   ![Seznam aplikací v pracovním prostoru](media/access-panel-workspaces/add-applications-list.png)

8. Vyberte kartu **Uživatelé a skupiny** . Chcete-li přidat uživatele nebo skupinu, vyberte možnost **Přidat uživatele**. 

9. Na stránce **Vybrat členy** vyberte uživatele nebo skupiny, kterým chcete pracovní prostor přiřadit. Případně můžete vyhledat uživatele nebo skupiny pomocí **vyhledávacího** pole.

   ![Přiřazení uživatelů a skupin k pracovnímu prostoru](media/access-panel-workspaces/add-users-and-groups.png)

10. Až budete hotovi s výběrem možnosti uživatelé a skupiny, zvolte **Vybrat**.

11. Chcete-li změnit roli uživatele z **oprávnění ke čtení** na **vlastníka** nebo naopak, klikněte na aktuální roli a vyberte novou roli.

    ![Přiřazení rolí uživatelům a skupinám](media/access-panel-workspaces/users-groups-list-role.png)

## <a name="view-audit-logs"></a>Zobrazení protokolů auditování

Protokoly auditu zaznamenávají operace s pracovními prostory moje aplikace, včetně akcí vytváření pracovních prostorů koncových uživatelů. Z mých aplikací se generují tyto události:

* Vytvoření pracovního prostoru
* Úprava pracovního prostoru
* Odstranit pracovní prostor
* Spuštění aplikace (koncový uživatel)
* Samoobslužné přidávání aplikací (koncový uživatel)
* Samoobslužné odstraňování aplikací (koncový uživatel)

Protokoly auditu můžete získat v [Azure Portal](https://portal.azure.com) tak, že vyberete **Azure Active Directory** > **podnikové aplikace** > **protokoly auditu** v části aktivita. V případě **služby**vyberte **Moje aplikace**.

   ![Přiřazení rolí uživatelům a skupinám](media/access-panel-workspaces/audit-log-myapps.png)

## <a name="get-support-for-my-account-pages"></a>Získat podporu pro stránky Můj účet

Na stránce Moje aplikace může uživatel vybrat **můj účet** > **Zobrazit můj účet** a otevřít jeho nastavení účtu. Na stránce **můj účet** Azure AD můžou uživatelé spravovat svoje bezpečnostní údaje, zařízení, hesla a další. Můžou také přistupovat ke svým nastavením účtu Office.

V případě, že potřebujete odeslat žádost o podporu k problému na stránce účtu Azure AD nebo na stránce účtu Office, postupujte podle těchto kroků, aby se vaše žádost správně směrovala: 

* Pokud máte problémy se stránkou **můj účet služby Azure AD** , otevřete žádost o podporu v rámci Azure Portal. Přejít na **Azure Portal** > **Azure Active Directory** > **nové žádosti o podporu**.

* Pokud máte problémy se stránkou **Office můj účet** , otevřete žádost o podporu v centru pro správu Microsoft 365. Přejít na **Centrum pro správu Microsoft 365** > **podporu**. 

## <a name="next-steps"></a>Další kroky
[Prostředí koncových uživatelů pro aplikace v Azure Active Directory](end-user-experiences.md)