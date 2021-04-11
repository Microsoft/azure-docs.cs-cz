---
title: Použití vzdálené plochy v Cloud Services (Rozšířená podpora)
description: Povolit vzdálenou plochu pro Cloud Services (Rozšířená podpora)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 94827916f28c9028d46bf7b5461a4fbd941b2a96
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104773398"
---
# <a name="apply-the-remote-desktop-extension-to-azure-cloud-services-extended-support"></a>Použití rozšíření Vzdálená plocha na Azure Cloud Services (Rozšířená podpora)

Azure Portal používá rozšíření vzdálené plochy k povolení vzdálené plochy i po nasazení aplikace. Nastavení vzdálené plochy pro cloudovou službu umožňuje povolit vzdálenou plochu, aktualizovat účet místního správce, vybrat certifikáty používané v ověřování a nastavit datum vypršení platnosti těchto certifikátů. 

## <a name="apply-remote-desktop--extension"></a>Použít rozšíření vzdálené plochy
1. Přejděte ke cloudové službě, pro kterou chcete povolit vzdálenou plochu, a v levém navigačním podokně vyberte **Vzdálená plocha** .

    :::image type="content" source="media/remote-desktop-1.png" alt-text="Image zobrazuje výběr možnosti Vzdálená plocha v Azure Portal":::

2. Vyberte **Přidat**.
3. Vyberte role, pro které se má povolit vzdálená plocha.
4. Vyplňte požadovaná pole pro uživatelské jméno, heslo, vypršení platnosti a certifikát (není nutné).
> Značte Heslo pro vzdálenou plochu musí být delší než 8-123 znaků a musí splňovat alespoň 3 požadavky na složitost hesla z následujících hodnot: 1) obsahuje velké písmeno 2) obsahuje malé písmeno 3), které obsahuje číselnou číslici 4). obsahuje speciální znak 5) řídicí znaky nejsou povoleny.

   :::image type="content" source="media/remote-desktop-2.png" alt-text="Obrázek ukazuje vložení informací potřebných pro připojení ke vzdálené ploše.":::

5. Po dokončení vyberte **Uložit**. Než budou instance rolí připravené na příjem připojení, bude chvíli trvat.

## <a name="connect-to-role-instances-with-remote-desktop-enabled"></a>Připojení k instancím rolí s povolenou vzdálenou plochou
Jakmile je na rolích povolena vzdálená plocha, můžete iniciovat připojení přímo z Azure Portal.

1. Kliknutím na **role a instance** otevřete nastavení instance.

    :::image type="content" source="media/remote-desktop-3.png" alt-text="Image zobrazuje výběr možnosti role a instance v okně konfigurace.":::

2. Vyberte instanci role, která má nakonfigurovanou vzdálenou plochu.
3. Kliknutím na **připojit** Stáhněte soubor připojení ke vzdálené ploše.

    :::image type="content" source="media/remote-desktop-4.png" alt-text="Image zobrazuje výběr instance role pracovního procesu v Azure Portal.":::
    
4. Otevřete soubor pro připojení k instanci role.


## <a name="next-steps"></a>Další kroky 
- Přečtěte si [požadavky na nasazení](deploy-prerequisite.md) pro Cloud Services (Rozšířená podpora).
- Přečtěte si [Nejčastější dotazy](faq.md) k Cloud Services (Rozšířená podpora).
- Nasaďte cloudovou službu (rozšířenou podporu) pomocí [Azure Portal](deploy-portal.md), [PowerShellu](deploy-powershell.md), [šablony](deploy-template.md) nebo sady [Visual Studio](deploy-visual-studio.md).
