---
title: Jak udělit oprávnění ke spravované identitě v pracovním prostoru Azure Synapse
description: Článek, který vysvětluje, jak nakonfigurovat oprávnění pro spravovanou identitu v pracovním prostoru Azure Synapse.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 9f519022fffe98c565c3b2d30f6578b9ebb70c57
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428014"
---
# <a name="grant-permissions-to-workspace-managed-identity-preview"></a>Udělení oprávnění spravované identitě pracovního prostoru (náhled)

Tento článek vás naučí udělit oprávnění ke spravované identitě v pracovním prostoru Azure synapse. Oprávnění zase umožňují přístup k fondům SQL v pracovním prostoru a účtu úložiště ADLS gen2 prostřednictvím portálu Azure.

>[!NOTE]
>Tato identita spravované pracovního prostoru bude označována jako spravovaná identita prostřednictvím zbytku tohoto dokumentu.

## <a name="grant-the-managed-identity--permissions-to-the-sql-pool"></a>Udělení oprávnění spravované identity fondu SQL

Spravovaná identita uděluje oprávnění fondům SQL v pracovním prostoru. S udělenými oprávněními můžete organizovat kanály, které provádějí aktivity související s fondem SQL. Když vytvoříte pracovní prostor Azure Synapse pomocí portálu Azure Portal, můžete udělit oprávnění správy identity CONTROL na fondy SQL.

Při vytváření pracovního prostoru Azure Synapse vyberte **Zabezpečení + sítě.** Pak vyberte **Udělit řízení spravované identitě pracovního prostoru ve fondech SQL**.

![Oprávnění CONTROL pro fondy SQL](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-16.png)

## <a name="grant-the-managed-identity-permissions-to-adls-gen2-storage-account"></a>Udělení oprávnění spravované identity účtu úložiště ADLS gen2

K vytvoření pracovního prostoru Azure Synapse je potřeba účet úložiště Gen2 ADLS. Aby bylo možné úspěšně spustit fondy Spark v pracovním prostoru Azure Synapse, potřebuje spravovaná identita Azure Synapse roli *přispěvatele dat objektů blob úložiště* na tomto účtu úložiště . Orchestrace kanálu v Azure Synapse také těží z této role.

### <a name="grant-permissions-to-managed-identity-during-workspace-creation"></a>Udělení oprávnění spravované identitě během vytváření pracovního prostoru

Azure Synapse se pokusí udělit roli přispěvatele dat objektů blob úložiště spravované identitě po vytvoření pracovního prostoru Azure Synapse pomocí portálu Azure. Podrobnosti o účtu úložiště Gen2 ADLS zadejte na kartě **Základy.**

![Karta Základy v toku vytváření pracovního prostoru](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-1.png)

V názvu **účtu** a **názvu souborového systému**zvolte účet úložiště ADLS gen2 a souborový systém .

![Poskytnutí podrobností o účtu úložiště Gen2 Systému ADLS](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-2.png)

Pokud tvůrce pracovního prostoru je také **vlastníkem** účtu úložiště ADLS gen2, pak Azure Synapse přiřadí roli *přispěvatele dat objektů blob úložiště* spravované identity. Pod zadanými údaji o účtu úložiště se zobrazí následující zpráva.

![Úspěšné přiřazení datového přispěvatele objektu blob úložiště](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-3.png)

Pokud tvůrce pracovního prostoru není vlastníkem účtu úložiště ADLS gen2, pak Azure Synapse nepřiřadí roli *přispěvatele dat objektů blob úložiště* spravované identitě. Zpráva zobrazená pod podrobnostmi účtu úložiště upozorní tvůrce pracovního prostoru, že nemá dostatečná oprávnění k udělení role *přispěvatele dat objektů blob úložiště* spravované identitě.

![Neúspěšné přiřazení datového přispěvatele objektů blob úložiště](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-4.png)

Jak se uvádí zpráva, nemůžete vytvářet fondy Spark, pokud *není přiřazen přispěvatel dat objektů blob úložiště* ke spravované identitě.

### <a name="grant-permissions-to-managed-identity-after-workspace-creation"></a>Udělení oprávnění spravované identitě po vytvoření pracovního prostoru

Pokud během vytváření pracovního prostoru nepřiřadíte *přispěvatele dat objektů blob úložiště* ke spravované identitě, vlastník účtu úložiště Gen2 ADLS tuto roli ručně přiřadí identitě. **Owner** Následující kroky vám pomohou provést ruční přiřazení.

#### <a name="step-1-navigate-to-the-adls-gen2-storage-account-in-azure-portal"></a>Krok 1: Přechod na účet úložiště Gen2 ADLS na webu Azure Portal

Na webu Azure Portal otevřete účet úložiště Gen2 ADLS a z levé navigace vyberte **Přehled.** Budete muset pouze přiřadit roli *přispěvatele dat objektů blob úložiště* na úrovni kontejneru nebo souborového systému. Vyberte **kontejnery**.  
![Přehled účtu úložiště ADLS gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-5.png)

#### <a name="step-2-select-the-container"></a>Krok 2: Vyberte kontejner

Spravovaná identita by měla mít přístup k datům ke kontejneru (systému souborů), který byl poskytnut při vytvoření pracovního prostoru. Tento kontejner nebo systém souborů najdete na webu Azure Portal. Otevřete pracovní prostor Azure Synapse na webu Azure Portal a v levé navigaci vyberte kartu **Přehled.**
![Kontejner účtu úložiště ADLS gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-7.png)


Vyberte stejný kontejner nebo systém souborů, chcete-li udělit roli *přispěvatele dat objektů blob úložiště* spravované identitě.
![Výběr kontejneru úložiště ADLS gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-6.png)

#### <a name="step-3-navigate-to-access-control"></a>Krok 3: Přechod na řízení přístupu

Vyberte **řízení přístupu (IAM).**

![Řízení přístupu (IAM)](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-8.png)

#### <a name="step-4-add-a-new-role-assignment"></a>Krok 4: Přidání nového přiřazení role

Vyberte **+ Přidat**.

![Přidání nového přiřazení role](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-9.png)

#### <a name="step-5-select-the-rbac-role"></a>Krok 5: Výběr role RBAC

Vyberte roli **přispěvatele dat objektů blob úložiště.**

![Výběr role RBAC](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-10.png)

#### <a name="step-6-select-the-azure-ad-security-principal"></a>Krok 6: Vyberte objekt zabezpečení Azure AD

V rozevíracím souboru **Vyberte** **uživatele, skupinu nebo instanční objekt služby Azure AD.**

![Vybrat objekt zabezpečení AAD](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-11.png)

#### <a name="step-7-search-for-the-managed-identity"></a>Krok 7: Hledání spravované identity

Název spravované identity je také název pracovního prostoru. Vyhledejte spravovanou identitu zadáním názvu pracovního prostoru Azure Synapse do **pole Vybrat**. Měli byste vidět spravovanou identitu v seznamu.

![Vyhledání spravované identity](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-12.png)

#### <a name="step-8-select-the-managed-identity"></a>Krok 8: Vyberte spravovanou identitu

Vyberte spravovanou identitu **pro vybrané členy**. Chcete-li přidat přiřazení role, vyberte **Uložit.**

![Výběr spravované identity](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-13.png)

#### <a name="step-9-verify-that-the-storage-blob-data-contributor-role-is-assigned-to-the-managed-identity"></a>Krok 9: Ověřte, zda je roli přispěvatele dat objektů blob úložiště přiřazena spravované identitě.

Vyberte **Řízení přístupu (IAM)** a pak vyberte **přiřazení rolí**.

![Ověření přiřazení role](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-14.png)

Spravovaná identita by se měla zobrazit v části **Přispěvatel dat objektů blob úložiště** s přiřazenou rolí *přispěvatele dat objektů blob úložiště.* 
![Výběr kontejneru úložiště ADLS gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-15.png)

## <a name="next-steps"></a>Další kroky

Další informace o [spravované identitě pracovního prostoru](./synapse-workspace-managed-identity.md)
