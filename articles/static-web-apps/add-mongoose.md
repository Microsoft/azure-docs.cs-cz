---
title: 'Kurz: přístup k datům v Cosmos DB pomocí Mongoose s využitím služby Azure static Web Apps'
description: Naučte se přistupovat k datům v Cosmos DB pomocí Mongoose z funkce Azure static Web Apps API.
author: GeekTrainer
ms.author: chrhar
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 01/25/2021
ms.openlocfilehash: f64cc67ad6f0296ad289d858795ee783943f3daf
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107260055"
---
# <a name="tutorial-access-data-in-cosmos-db-using-mongoose-with-azure-static-web-apps"></a>Kurz: přístup k datům v Cosmos DB pomocí Mongoose s využitím služby Azure static Web Apps

[Mongoose](https://mongoosejs.com/) je nejoblíbenější klient služby ODM (mapování dokumentu objektů) pro Node.js. Díky tomu, abyste mohli navrhovat datovou strukturu a vynutili ověřování, Mongoose poskytuje všechny nástroje, které jsou nezbytné pro interakci s databázemi, které podporují rozhraní Mongoose API. [Cosmos DB](../cosmos-db/mongodb-introduction.md) podporuje nezbytná rozhraní API Mongoose a je k dispozici jako možnost back-end serveru v Azure.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> - Vytvoření účtu s Cosmos DB bez serveru
> - Vytvoření statického Web Apps Azure
> - Aktualizovat nastavení aplikace pro uložení připojovacího řetězce

Pokud nemáte předplatné Azure, vytvořte si [bezplatný zkušební účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

- [Účet Azure](https://azure.microsoft.com/free/)
- [Účet GitHub](https://github.com/join)

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na [Azure Portal](https://portal.azure.com).

## <a name="create-a-cosmos-db-serverless-database"></a>Vytvoření databáze Cosmos DB bez serveru

Začněte vytvořením účtu [Cosmos DB bez serveru](https://docs.microsoft.com/azure/cosmos-db/serverless) . Pomocí účtu bez serveru platíte jenom za prostředky, které se používají, a vyhnout se tak nutnosti vytvořit úplnou infrastrukturu.

1. Přejít na [https://portal.azure.com](https://portal.azure.com)
2. Klikněte na **vytvořit prostředek** .
3. Do vyhledávacího pole zadejte **Azure Cosmos DB**
4. Klikněte na **Azure Cosmos DB**
5. Klikněte na **Vytvořit**.
6. Pomocí následujících informací nakonfigurujte účet Azure Cosmos DB.
    - Předplatné: vyberte předplatné, které chcete použít.
    - Prostředek: klikněte na **vytvořit nový** a nastavte název na **ASWA-Mongoose** .
    - Název účtu: je potřeba zadat jedinečnou hodnotu.
    - API: **Azure Cosmos DB pro rozhraní API MongoDB**
    - Poznámkové bloky (Preview): **vypnuto**
    - Umístění: **západní USA 2**
    - Režim kapacity: bez **serveru (Preview)**
    - Verze: **3,6**
    - Zóny dostupnosti: **Zakázat** 
 :::image type="content" source="media/add-mongoose/cosmos-db.png" alt-text="Vytvoření nové instance Cosmos DB":::
7. Klikněte na tlačítko **zkontrolovat + vytvořit** .
8. Klikněte na **Vytvořit**.

Proces vytváření bude trvat několik minut. Později se kroky vrátí do databáze pro shromáždění připojovacího řetězce.

## <a name="create-a-static-web-app"></a>Vytvoření statické webové aplikace

V tomto kurzu se používá úložiště šablon GitHubu, které vám pomůžou vytvořit aplikaci.

1. Přejít na [úvodní šablonu](https://github.com/login?return_to=/staticwebdev/mongoose-starter/generate)
2. Zvolit **vlastníka** (Pokud používáte jinou organizaci než hlavní účet)
3. Pojmenování úložiště **ASWA-Mongoose-tutorial**
4. Klikněte na **vytvořit úložiště ze šablony** .
5. Vrátit se k [Azure Portal](https://portal.azure.com)
6. Klikněte na **vytvořit prostředek** .
7. Do vyhledávacího pole zadejte **statické webové aplikace**
8. Vybrat **statickou webovou aplikaci (Preview)**
9. Klikněte na **Vytvořit**.
10. Pomocí následujících informací nakonfigurujte statickou webovou aplikaci Azure.
    - Předplatné: vyberte stejné předplatné jako předtím.
    - Skupina prostředků: vyberte **ASWA-Mongoose**
    - Název: **ASWA-Mongoose-kurz**
    - Oblast: **západní USA 2**
    - Klikněte na **Přihlásit se pomocí GitHubu** .
    - Pokud se zobrazí výzva, abyste umožnili službě Azure static Web Apps vytvořit akci GitHubu pro povolení nasazení, klikněte na **autorizovat** .
    - Organizace: název účtu
    - Úložiště: **ASWA-Mongoose – kurz**
    - Větev: **Hlavní**
    - Přednastavení sestavení: zvolit **vlastní**
    - Umístění aplikace: **/Public**
    - Umístění rozhraní API: **rozhraní API**
    - Umístění artefaktu aplikace: *ponechte prázdný* 
     :::image type="content" source="media/add-mongoose/azure-static-web-apps.png" alt-text="dokončený Web Apps statických formulářů Azure"::: .
11. Klikněte na tlačítko **zkontrolovat a vytvořit**
12. Klikněte na **Vytvořit**.

Proces vytváření trvá několik minut, ale po zřízení aplikace můžete kliknout na **Přejít k prostředku** .

## <a name="configure-database-connection-string"></a>Konfigurovat připojovací řetězec databáze

Aby mohla webová aplikace komunikovat s databází, připojovací řetězec databáze je uložen jako nastavení aplikace. Hodnoty nastavení jsou dostupné v Node.js pomocí `process.env` objektu.

1. V levém horním rohu Azure Portal klikněte na **Domů** (nebo přejděte zpátky na [https://portal.azure.com](https://portal.azure.com) ).
2. Klikněte na **skupiny prostředků** .
3. Klikněte na **ASWA-Mongoose** .
4. Klikněte na název vašeho databázového účtu – bude mít typ **Azure Cosmos DB účet** .
5. V části **Nastavení** klikněte na **připojovací řetězec** .
6. Zkopírování připojovacího řetězce uvedeného v části **primární připojovací řetězec**
7. V popisu cesty klikněte na **ASWA-Mongoose** .
8. Kliknutím na **ASWA-Mongoose – kurz** se vrátíte k instanci webu.
9. V části **Nastavení** klikněte na **Konfigurace** .
10. Klikněte na **Přidat** a vytvořte nové nastavení aplikace s následujícími hodnotami.
    - Název: **CONNECTION_STRING**
    - Hodnota: vložte připojovací řetězec, který jste zkopírovali dříve.
11. Klikněte na tlačítko **OK**.
12. Klikněte na **Uložit**.

## <a name="navigate-to-your-site"></a>Přejít na web

Teď můžete prozkoumat statickou webovou aplikaci.

1. Klikněte na **Přehled** .
1. Klikněte na adresu URL zobrazenou vpravo nahoře.
    1. Bude vypadat podobně jako `https://calm-pond-05fcdb.azurestaticapps.net`
1. Vytvořte nový úkol zadáním názvu a kliknutím na **Přidat úlohu** .
1. Potvrďte, že se úloha zobrazuje (může to chvíli trvat).
1. Označte úkol jako dokončený **kliknutím na zaškrtávací políčko** .
1. **Aktualizujte stránku** a potvrďte, že se používá databáze.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte skupinu prostředků pomocí následujících kroků:

1. Vrátit se k [Azure Portal](https://portal.azure.com)
2. Klikněte na **skupiny prostředků** .
3. Klikněte na **ASWA-Mongoose** .
4. Klikněte na **Odstranit skupinu prostředků** .
5. Do textového pole zadejte **ASWA-Mongoose** .
6. Klikněte na **Odstranit**.

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku, kde se dozvíte, jak nakonfigurovat místní vývoj...
> [!div class="nextstepaction"]
> [Nastavení místního vývoje](./local-development.md)
 
