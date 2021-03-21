---
title: zahrnout soubor
description: " – soubor"
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: mingshen-ms
ms.author: krsh
ms.date: 10/20/2020
ms.openlocfilehash: c60d2a9b13cce9251ff0f730081a9d677206770d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102630098"
---
## <a name="generalize-the-image"></a>Generalizace bitové kopie

Všechny obrázky v Azure Marketplace musí být obecně znovu použitelné. K tomu je potřeba, aby byl virtuální pevný disk operačního systému zobecněný, operace, která odebere všechny identifikátory jednotlivých instancí a softwarové ovladače z virtuálního počítače.

### <a name="for-windows"></a>Pro Windows

Disky s operačním systémem Windows jsou zobecněny pomocí nástroje [Sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) . Pokud později aktualizujete nebo znovu nakonfigurujete operační systém, musíte znovu spustit nástroj Sysprep.

> [!WARNING]
> Po spuštění nástroje Sysprep vypněte virtuální počítač, dokud nebude nasazen, protože aktualizace mohou být spouštěny automaticky. Toto vypnutí zabrání následným aktualizacím v provádění změn specifických pro konkrétní instance v operačním systému nebo nainstalovaných službách. Další informace o spuštění nástroje Sysprep najdete v tématu [postup generalizace VHD](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep).

### <a name="for-linux"></a>Pro Linux

Následující proces generalizuje virtuální počítač Linux a znovu ho nasadí jako samostatný virtuální počítač. Podrobnosti najdete v tématu [Postup vytvoření image virtuálního počítače nebo virtuálního pevného disku](../../virtual-machines/linux/capture-image.md). V případě, že se dostanete k části s názvem vytvoření virtuálního počítače ze zaznamenané bitové kopie, můžete zastavit.

1. Odeberte agenta Azure Linux.
    1. Připojte se k VIRTUÁLNÍmu počítači se systémem Linux pomocí klienta SSH.
    2. V okně SSH zadejte tento příkaz: `sudo waagent –deprovision+user` .
    3. Zadejte Y pro pokračování (můžete přidat parametr-Force k předchozímu příkazu, abyste se vyhnuli potvrzovacímu kroku).
    4. Po dokončení příkazu zadejte **Exit** a zavřete tak klienta ssh.
2. Zastavte virtuální počítač.
    1. V Azure Portal vyberte skupinu prostředků (RG) a zrušte přidělení virtuálního počítače.
    2. Váš virtuální počítač je teď zobecněný a můžete vytvořit nový virtuální počítač pomocí tohoto disku virtuálního počítače.

### <a name="capture-image"></a>Zachytit bitovou kopii

Jakmile je váš virtuální počítač připravený, můžete ho zachytit v galerii sdílených imagí Azure. Zachyťte postup podle následujících kroků:

1. Na [Azure Portal](https://ms.portal.azure.com/)přejdete na stránku svého virtuálního počítače.
2. Vyberte **zaznamenat**.
3. V části **sdílet image do galerie sdílených imagí** vyberte **Ano, sdílejte ji do galerie jako verzi image**.
4. V části **stav operačního systému** vyberte generalizovaná.
5. Vyberte cílovou galerii imagí nebo **vytvořte novou**.
6. Vyberte definici cílové Image nebo **vytvořte novou**.
7. Zadejte **číslo verze** image.
8. Vyberte **zkontrolovat + vytvořit** a zkontrolujte své volby.
9. Po úspěšném ověření vyberte **vytvořit**.

Aby bylo možné publikovat, musí mít účet vydavatele přístup vlastníka ke SIG. Udělení přístupu:

1. Přejít do galerie sdílených imagí.
2. Na levém panelu vyberte **řízení přístupu** (IAM).
3. Vyberte **Přidat** a pak **Přidat přiřazení role**.
4. Vyberte **roli** nebo **vlastníka**.
5. V části **přiřadit přístup k** vybranému **uživateli, skupině nebo objektu služby**.
6. Vyberte e-mail Azure osoby, která bude publikovat image.
7. Vyberte **Uložit**.

:::image type="content" source="../media/create-vm/add-role-assignment.png" alt-text="Zobrazí okno Přidat přiřazení role.":::

> [!NOTE]
> Nemusíte generovat identifikátory URI SAS, protože teď můžete publikovat v partnerském centru image SIG. Pokud však stále potřebujete odkazovat na kroky generování identifikátoru URI SAS, přečtěte si téma [jak vygenerovat identifikátor URI SAS pro image virtuálního počítače](../azure-vm-get-sas-uri.md).
