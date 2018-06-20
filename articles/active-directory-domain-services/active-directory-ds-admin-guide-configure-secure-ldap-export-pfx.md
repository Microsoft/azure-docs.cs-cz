---
title: Konfigurace zabezpečeného LDAP (LDAPS) ve službě Azure AD Domain Services | Microsoft Docs
description: Konfigurace zabezpečení protokolu LDAP (LDAPS) pro spravované doméně služby Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: maheshu
ms.openlocfilehash: d2c7bd8b335ce49bed8e39812cccbe7ab474bf8f
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36211522"
---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Konfigurace zabezpečeného LDAP (LDAPS) pro spravované doméně služby Azure AD Domain Services

## <a name="before-you-begin"></a>Než začnete
Ujistěte se, když jste dokončili [úloha 1 – získání certifikátu pro zabezpečený LDAP](active-directory-ds-admin-guide-configure-secure-ldap.md).


## <a name="task-2---export-the-secure-ldap-certificate-to-a-pfx-file"></a>Úloha 2 – zabezpečený LDAP certifikát, který chcete exportovat. Soubor PFX
Než začnete tuto úlohu, ujistěte se, získání certifikátu zabezpečeného LDAP od veřejné certifikační autority nebo jste vytvořili certifikát podepsaný svým držitelem.

Proveďte následující kroky, LDAPS certifikát, který chcete exportovat. Soubor PFX.

1. Stiskněte **spustit** tlačítko a typ **R**. V **spustit** dialogové okno, typ **konzoly mmc** a klikněte na tlačítko **OK**.

    ![Spustit konzolu MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-start-run.png)
2. Na **řízení uživatelských účtů** výzva, klikněte na tlačítko **Ano** ke spuštění konzoly MMC (Microsoft Management Console) jako správce.
3. Z **soubor** nabídky, klikněte na tlačítko **přidat nebo odebrat modul Snap-in...** .

    ![Přidání modulu snap-in konzoly MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-add-snapin.png)
4. V **přidat nebo odebrat moduly Snap in** dialogovém okně, vyberte **certifikáty** modul snap-in a klikněte na tlačítko **Přidat >** tlačítko.

    ![Přidání modulu snap-in Certifikáty konzoly MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin.png)
5. V **modul snap-in Certifikáty** průvodci vyberte **účet počítače** a klikněte na tlačítko **Další**.

    ![Přidat modul snap-in Certifikáty pro účet počítače](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-computer-account.png)
6. Na **vybrat počítač** vyberte **místní počítač: (počítač je spuštěna tato konzola)** a klikněte na tlačítko **Dokončit**.

    ![Přidat modul snap-in Certifikáty - vyberte počítače](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-local-computer.png)
7. V **přidat nebo odebrat moduly Snap in** dialogové okno, klikněte na tlačítko **OK** přidat modul snap-in Certifikáty konzoly MMC.

    ![Přidat modul snap-in Certifikáty konzoly MMC - provést](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin-done.png)
8. V okně konzoly MMC kliknutím rozbalte **kořenový adresář konzoly**. Měli byste vidět modulu snap-in Certifikáty načíst. Klikněte na tlačítko **certifikáty (místní počítač)** rozbalte. Kliknutím rozbalte položku **osobní** uzlu, za nímž následuje **certifikáty** uzlu.

    ![Úložiště otevřete osobních certifikátů](./media/active-directory-domain-services-admin-guide/secure-ldap-open-personal-store.png)
9. Měli byste vidět certifikát podepsaný svým držitelem, které jsme vytvořili. Můžete prozkoumat vlastnosti certifikátu, ujistěte se, že kryptografický otisk se shoduje s hlášené v prostředí PowerShell systému windows, když jste vytvořili certifikát.
10. Vyberte certifikát podepsaný svým držitelem a **klikněte pravým tlačítkem na**. V místní nabídce vyberte **všechny úlohy** a vyberte **exportovat...** .

    ![Export certifikátu](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert.png)
11. V **Průvodce exportem certifikátu**, klikněte na tlačítko **Další**.

    ![Průvodce exportem certifikátu](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert-wizard.png)
12. Na **exportovat soukromý klíč** vyberte **Ano, exportovat soukromý klíč**a klikněte na tlačítko **Další**.

    ![Exportovat privátní klíč certifikátu](./media/active-directory-domain-services-admin-guide/secure-ldap-export-private-key.png)

    > [!WARNING]
    > Je nutné exportovat privátní klíč společně se certifikát. Pokud zadáte soubor PFX, který neobsahuje privátní klíč pro certifikát, povolení zabezpečeného LDAP vaší spravované domény nezdaří.
    >
    >
13. Na **formát souboru pro Export** vyberte **Personal Information Exchange – PKCS #12 (. PFX)** jako formát souboru pro exportovaný certifikát.

    ![Formát souboru pro export certifikátu](./media/active-directory-domain-services-admin-guide/secure-ldap-export-to-pfx.png)

    > [!NOTE]
    > Pouze. Formát souboru PFX je podporován. Neexportovat certifikát, který chcete. Formát souboru CER.
    >
    >
14. Na **zabezpečení** vyberte **heslo** možnost a zadejte heslo k ochraně. Soubor PFX. Heslo si zapamatujte, protože bude potřeba v dalším úkolem. Klikněte na tlačítko **Další** pokračovat.

    ![Heslo pro export certifikátu ](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-password.png)

    > [!NOTE]
    > Toto heslo si poznamenejte. Je třeba při povolování zabezpečený LDAP pro tuto doménu spravovaných v [úloha 3 – povolení zabezpečeného LDAP pro spravované doméně](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)
    >
    >
15. Na **soubor pro Export** stránky, zadejte název souboru a umístění, kde chcete exportovat certifikát.

    ![Cesta pro export certifikátu](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-path.png)
16. Na následující stránce, klikněte na tlačítko **Dokončit** a vyexportovat si certifikát do souboru PFX. Pokud byla exportována certifikát, měli byste vidět dialogové okno potvrzení.

    ![Export certifikátu provést](./media/active-directory-domain-services-admin-guide/secure-ldap-exported-as-pfx.png)


## <a name="next-step"></a>Další krok
[Úloha 3 – povolení zabezpečeného LDAP pro spravované doméně](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)
