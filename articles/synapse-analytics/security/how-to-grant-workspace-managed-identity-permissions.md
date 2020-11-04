---
title: Udělení oprávnění spravované identitě v pracovním prostoru synapse
description: Článek, který vysvětluje, jak nakonfigurovat oprávnění pro spravovanou identitu v pracovním prostoru Azure synapse.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 54612bee5715cdb78141a8aacfa5d24c814269d1
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93312378"
---
# <a name="grant-permissions-to-workspace-managed-identity-preview"></a>Udělení oprávnění pro spravovanou identitu pracovního prostoru (Preview)

V tomto článku se naučíte, jak udělit oprávnění ke spravované identitě v pracovním prostoru Azure synapse. Oprávnění zase umožňují přístup k vyhrazeným fondům SQL v pracovním prostoru a ADLS Gen2 účtu úložiště prostřednictvím Azure Portal.

>[!NOTE]
>Tato identita spravovaná pracovním prostorem se bude v ostatních částech tohoto dokumentu označovat jako spravovaná identita.

## <a name="grant-managed-identity-permissions-to-the-dedicated-sql-pool"></a>Udělit spravovanému fondu SQL oprávnění spravovaných identit

Spravovaná identita uděluje oprávnění k vyhrazeným fondům SQL v pracovním prostoru. S udělenými oprávněními můžete orchestrovat kanály, které provádějí vyhrazené aktivity související s fondem SQL. Když vytvoříte pracovní prostor Azure synapse pomocí Azure Portal, můžete udělit oprávnění spravovat spravovaná řízení identit na vyhrazené fondy SQL.

Při vytváření pracovního prostoru Azure synapse vyberte **zabezpečení** . Pak **pro přístup k fondům SQL vyberte možnost povoluje kanálům (spouštěným jako identitu přiřazené systémem v pracovním prostoru)**.

![ŘÍZENÍ oprávnění pro vyhrazené fondy SQL](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-16.png)

## <a name="grant-the-managed-identity-permissions-to-adls-gen2-storage-account"></a>Udělení oprávnění spravovaných identit ADLS Gen2 účtu úložiště

K vytvoření pracovního prostoru Azure synapse se vyžaduje účet úložiště ADLS Gen2. Aby bylo možné úspěšně spustit fondy Spark v pracovním prostoru Azure synapse, musí mít spravovaná identita Azure synapse na tomto účtu úložiště roli *Přispěvatel dat objektů BLOB úložiště* . Orchestrace kanálu ve službě Azure synapse také přináší výhody této role.

### <a name="grant-permissions-to-managed-identity-during-workspace-creation"></a>Udělení oprávnění spravované identitě během vytváření pracovního prostoru

Služba Azure synapse se pokusí udělit roli Přispěvatel dat objektů BLOB úložiště spravované identitě po vytvoření pracovního prostoru Azure synapse pomocí Azure Portal. Podrobnosti o ADLS Gen2 účtu úložiště zadáte na kartě **základy** .

![Karta základy v toku vytváření pracovního prostoru](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-1.png)

V poli **název účtu** a **název systému souborů** vyberte adls Gen2 účet úložiště a systém souborů.

![Poskytnutí podrobností o ADLS Gen2 účtu úložiště](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-2.png)

Pokud je Autor pracovního prostoru také **vlastníkem** adls Gen2 účtu úložiště, pak Azure synapse přiřadí roli *Přispěvatel dat objektů BLOB úložiště* ke spravované identitě. Pod podrobnostmi účtu úložiště, které jste zadali, se zobrazí následující zpráva.

![Úspěšné přiřazení přispěvatele dat objektu BLOB úložiště](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-3.png)

Pokud není tvůrce pracovního prostoru vlastníkem ADLS Gen2 účtu úložiště, pak Azure synapse nepřiřazuje roli *Přispěvatel dat objektů BLOB úložiště* ke spravované identitě. Zpráva zobrazená pod podrobnostmi účtu úložiště upozorní autora pracovního prostoru, že nemají dostatečná oprávnění pro udělení role *Přispěvatel dat objektu BLOB úložiště* spravované identitě.

![Neúspěšné přiřazení přispěvatele dat objektu BLOB úložiště](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-4.png)

V případě stavů zpráv nemůžete vytvořit fondy Spark, pokud není *Přispěvatel dat objektů BLOB úložiště* přiřazen ke spravované identitě.

### <a name="grant-permissions-to-managed-identity-after-workspace-creation"></a>Udělení oprávnění spravované identitě po vytvoření pracovního prostoru

Pokud při vytváření pracovního prostoru nepřiřadíte *přispěvateli dat objektů BLOB úložiště* ke spravované identitě, pak **vlastník** účtu úložiště adls Gen2 ručně přiřadí tuto roli k identitě. Následující kroky vám pomůžou provést ruční přiřazení.

#### <a name="step-1-navigate-to-the-adls-gen2-storage-account-in-azure-portal"></a>Krok 1: přechod na účet úložiště ADLS Gen2 v Azure Portal

V Azure Portal otevřete ADLS Gen2 účet úložiště a v levém navigačním panelu vyberte **Přehled** . Roli *Přispěvatel dat objektů BLOB úložiště* budete muset přiřadit jenom na úrovni kontejneru nebo systému souborů. Vyberte **kontejnery**.  
![Přehled ADLS Gen2 účtu úložiště](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-5.png)

#### <a name="step-2-select-the-container"></a>Krok 2: Výběr kontejneru

Spravovaná identita by měla mít přístup k datům do kontejneru (systému souborů), který byl poskytnut při vytvoření pracovního prostoru. Tento kontejner nebo systém souborů najdete v Azure Portal. Otevřete v Azure Portal pracovní prostor Azure synapse a na levém navigačním panelu vyberte kartu **Přehled** .
![ADLS Gen2 kontejner účtu úložiště](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-7.png)


Vyberte stejný kontejner nebo systém souborů pro udělení role *Přispěvatel dat objektů BLOB úložiště* spravované identitě.
![Snímek obrazovky se zobrazením kontejneru nebo systému souborů, který byste měli vybrat.](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-6.png)

#### <a name="step-3-navigate-to-access-control"></a>Krok 3: přechod na řízení přístupu

Vyberte **Access Control (IAM)**.

![Řízení přístupu (IAM)](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-8.png)

#### <a name="step-4-add-a-new-role-assignment"></a>Krok 4: přidání přiřazení nové role

Vyberte **+ Přidat**.

![Přidat nové přiřazení role](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-9.png)

#### <a name="step-5-select-the-azure-role"></a>Krok 5: výběr role Azure

Vyberte roli **Přispěvatel dat objektu BLOB úložiště** .

![Výběr role Azure](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-10.png)

#### <a name="step-6-select-the-azure-ad-security-principal"></a>Krok 6: Vyberte objekt zabezpečení služby Azure AD.

V rozevíracím seznamu **přiřadit přístup** vyberte **uživatele, skupinu nebo instanční objekt služby Azure AD** .

![Vybrat objekt zabezpečení AAD](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-11.png)

#### <a name="step-7-search-for-the-managed-identity"></a>Krok 7: Vyhledání spravované identity

Název spravované identity je zároveň názvem pracovního prostoru. Vyhledejte spravovanou identitu zadáním názvu pracovního prostoru Azure synapse v **Select**. Měla by se zobrazit uvedená spravovaná identita.

![Najít spravovanou identitu](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-12.png)

#### <a name="step-8-select-the-managed-identity"></a>Krok 8: vyberte spravovanou identitu.

Vyberte spravovanou identitu pro **Vybrané členy**. Vyberte **Uložit** a přidejte přiřazení role.

![Vyberte spravovanou identitu.](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-13.png)

#### <a name="step-9-verify-that-the-storage-blob-data-contributor-role-is-assigned-to-the-managed-identity"></a>Krok 9: Ověřte, jestli je role Přispěvatel dat objektu BLOB úložiště přiřazená k spravované identitě.

Vyberte **Access Control (IAM)** a pak vyberte **přiřazení rolí**.

![Ověřit přiřazení role](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-14.png)

Vaše spravovaná identita by se měla zobrazit v části **Přispěvatel dat objektů BLOB úložiště** s přiřazenou rolí *Přispěvatel dat objektu BLOB služby Storage* . 
![ADLS Gen2 výběru kontejneru účtu úložiště](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-15.png)

## <a name="next-steps"></a>Další kroky

Další informace o [identitě spravované v pracovním prostoru](./synapse-workspace-managed-identity.md)
