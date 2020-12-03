---
title: Jak kontrolovat soubory Azure
description: V této příručce najdete podrobné informace o tom, jak kontrolovat soubory Azure.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/01/2020
ms.openlocfilehash: a0bd7a4cd8afafc16f05b4a37cd5723304ad931e
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553120"
---
# <a name="register-and-scan-azure-files"></a>Registrace a kontrola souborů Azure

## <a name="supported-capabilities"></a>Podporované možnosti

Soubory Azure podporují úplné a přírůstkové kontroly pro zachycení metadat a použití klasifikací v metadatech na základě klasifikací systému a zákazníka.

## <a name="prerequisites"></a>Předpoklady

- Před registrací zdrojů dat vytvořte účet Azure dosah. Další informace o vytvoření účtu dosah najdete v tématu [rychlý Start: vytvoření účtu Azure dosah](create-catalog-portal.md).
- Pro nastavení a naplánování kontrol musíte být správcem zdroje dat. Podrobnosti najdete v tématu [oprávnění katalogu](catalog-permissions.md) .

## <a name="register-an-azure-files-storage-account"></a>Registrace účtu úložiště služby soubory Azure

Pokud chcete zaregistrovat nový účet služby soubory Azure v katalogu dat, udělejte toto:

1. Přejděte do dosah Data Catalog.
1. Na levém navigačním panelu vyberte **Centrum pro správu** .
1. Vyberte **zdroje dat** v části **zdroje a skenování**.
1. Vyberte **+ Nový**.
1. V nabídce **Registrovat zdroje** vyberte **soubory Azure**. Vyberte **Pokračovat**.

:::image type="content" source="media/register-scan-azure-files/register-new-data-source.png" alt-text="registrovat nový zdroj dat" border="true":::

Na obrazovce **Registrovat zdroje (soubory Azure)** udělejte toto:

1. Zadejte **název** , se kterým bude zdroj dat uveden v katalogu.
1. Vyberte, jak chcete odkazovat na požadovaný účet úložiště:
   1. Vyberte **z předplatného Azure**, vyberte příslušné předplatné z rozevíracího seznamu **předplatné Azure** a příslušný účet úložiště z rozevíracího seznamu **název účtu úložiště** .
   1. Případně můžete vybrat možnost **zadat ručně** a zadat koncový bod služby (URL).
1. **Dokončete** registraci zdroje dat.

:::image type="content" source="media/register-scan-azure-files/register-sources.png" alt-text="Možnosti registrace zdrojů" border="true":::

## <a name="set-up-authentication-for-a-scan"></a>Nastavení ověřování pro kontrolu

Pokud chcete nastavit ověřování pro úložiště souborů Azure pomocí klíče účtu, udělejte toto:

1. Jako **klíč účtu** vyberte metodu ověřování.
2. Vyberte možnost **z předplatného Azure** .
3. Vyberte předplatné Azure, kde existuje účet služby soubory Azure.
4. Ze seznamu vyberte název svého účtu úložiště.
5. Klikněte na **Finish** (Dokončit).

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Další kroky

- [Procházet Azure dosah Data Catalog](how-to-browse-catalog.md)
- [Hledání ve službě Azure dosah Data Catalog](how-to-search-catalog.md)