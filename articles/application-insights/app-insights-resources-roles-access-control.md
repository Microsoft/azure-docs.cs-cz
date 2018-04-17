---
title: Prostředky, rolí a řízení přístupu ve službě Azure Application Insights | Microsoft Docs
description: Vlastníci, přispěvatelé a čtenáři z vaší organizace statistiky.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 49f736a5-67fe-4cc6-b1ef-51b993fb39bd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: mbullwin
ms.openlocfilehash: 5bb6c33c083e1be690b70b63ca087c923d43f3ee
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="resources-roles-and-access-control-in-application-insights"></a>Prostředky, role a řízení přístupu ve službě Application Insights
Můžete řídit, kdo má číst a aktualizovat přístupu k datům v Azure [Application Insights][start], pomocí [řízení přístupu na základě Role ve službě Microsoft Azure](../role-based-access-control/role-assignments-portal.md).

> [!IMPORTANT]
> Přiřadit uživatelům v přístupu **skupiny prostředků nebo předplatného** , ke které prostředek vaší aplikace patří - není v prostředku sám sebe. Přiřazení **Přispěvatel součásti Application Insights** role. Tím se zajistí uniform řízení přístupu k webové testy a výstrahy společně s prostředek vaší aplikace. [Další informace](#access).
> 
> 

## <a name="resources-groups-and-subscriptions"></a>Prostředky, skupiny a odběry
První, definice:

* **Prostředek** – instance služby Microsoft Azure. Prostředku Application Insights shromažďuje, analyzuje a zobrazuje telemetrická data odeslaná z vaší aplikace.  Ostatní typy prostředků Azure obsahují webové aplikace, databáze a virtuální počítače.
  
    Chcete-li zobrazit vaše prostředky, otevřete [portálu Azure][portal], přihlaste se a klikněte na možnost všechny prostředky. Najít prostředek, napište část názvu do pole filtru.
  
    ![Seznam prostředků Azure](./media/app-insights-resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**Skupina prostředků** ] [ group] -každý prostředek patří do jedné skupiny. Skupina je pohodlný způsob, jak spravovat související prostředky, zejména pro řízení přístupu. Do jedné skupiny prostředků může například dávat webovou aplikaci, prostředek Application Insights pro sledování aplikace a prostředků úložiště, aby exportovaná data.

    ![Vyberte možnost Procházet, skupiny prostředků, a potom vyberte skupinu](./media/app-insights-resources-roles-access-control/11-group.png)

* [**Předplatné** ](https://portal.azure.com) – Pokud chcete použít Application Insights nebo další prostředky Azure, přihlaste se k předplatnému Azure. Každá skupina prostředků patří do jedno předplatné, kde vyberete vašeho balíčku ceny a, pokud je předplatné organizace, zvolte členy a jejich oprávnění k přístupu.
* [**Účet Microsoft** ] [ account] -uživatelské jméno a heslo, které používáte pro přihlášení k Microsoft Azure odběry, XBox Live, Outlook.com a dalších služeb společnosti Microsoft.

## <a name="access"></a> Řízení přístupu ve skupině prostředků
Je důležité si uvědomit, že kromě prostředek, který jste vytvořili pro vaši aplikaci, existují také samostatné skrytá prostředky pro výstrahy a webové testy. Jsou připojené ke stejné [skupiny prostředků](#resource-group) jako aplikace. Můžete také mít ukládat jinými službami Azure zde například weby nebo úložiště.

![Prostředky ve službě Application Insights](./media/app-insights-resources-roles-access-control/00-resources.png)

K řízení přístupu na tyto prostředky se proto doporučuje:

* Řízení přístupu na **skupiny prostředků nebo předplatného** úroveň.
* Přiřazení **součást Statistika aplikace Přispěvatel** role pro uživatele. To umožňuje upravovat webové testy, výstrahy a prostředky Application Insights, bez poskytnutí přístupu k jiným službám ve skupině.

## <a name="to-provide-access-to-another-user"></a>K poskytování přístupu k jinému uživateli
Musí mít oprávnění vlastníka pro předplatné nebo skupinu prostředků.

Uživatel musí mít [Account Microsoft][account], nebo přístup k jejich [Account organizace Microsoft](../active-directory/sign-up-organization.md). Zajistíte přístup jednotlivcům a taky do skupin uživatelů, které jsou definované v Azure Active Directory.

#### <a name="navigate-to-the-resource-group"></a>Přejděte do skupiny prostředků
Přidejte uživatele existuje.

![V okně prostředků aplikace v otevřít Essentials, otevřete skupinu prostředků a existuje vyberte uživatele, nebo nastavení. Klikněte na Přidat.](./media/app-insights-resources-roles-access-control/01-add-user.png)

Nebo můžete přejít do jiné úrovně a přidejte uživatele k předplatnému.

#### <a name="select-a-role"></a>Výběr role
![Vyberte role pro nového uživatele](./media/app-insights-resources-roles-access-control/03-role.png)

| Role | Ve skupině prostředků |
| --- | --- |
| Vlastník |Můžete změnit všechno včetně přístupu uživatele |
| Přispěvatel |Můžete upravit jakýkoli, včetně všech prostředků |
| Přispěvatel Statistika součásti aplikace |Můžete upravit prostředky Application Insights, webové testy a upozornění |
| Čtenář |Můžete zobrazit, ale není nic nezmění. |

'Úpravy' zahrnuje vytváření, odstraňování a aktualizace:

* Zdroje a prostředky
* Webové testy
* Výstrahy
* Průběžný export

#### <a name="select-the-user"></a>Vybrat uživatele

Pokud uživatel, který má být není v adresáři, můžete pozvat, každý uživatel s účtem Microsoft.
(Pokud používají služby, jako je Outlook.com, OneDrive, Windows Phone nebo XBox Live, budou mít účet Microsoft.)

## <a name="related-content"></a>Související obsah

* [Řízení přístupu v Azure na základě role](../role-based-access-control/role-assignments-portal.md)

<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../azure-resource-manager/resource-group-overview.md
[portal]: https://portal.azure.com/
[start]: app-insights-overview.md
