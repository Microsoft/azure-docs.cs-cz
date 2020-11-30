---
title: Nastavení integrace AWS se službou Azure Cost Management
description: Tento článek vás provede nastavením a konfigurací integrace sestavy nákladů a využití AWS se službou Azure Cost Management.
author: bandersmsft
ms.author: banders
ms.date: 10/23/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: matrive
ms.openlocfilehash: 2b8a008decc41a5686fb2c8d9fee271f95f0fef3
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96122402"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>Nastavení a konfigurace integrace sestavy nákladů a využití AWS

Díky integraci sestavy nákladů a využití (CUR) AWS (Amazon Web Services) můžete monitorovat a řídit své útraty AWS ve službě Azure Cost Management. Integrace umožňuje využít jedno umístění na portálu Azure Portal, odkud můžete monitorovat a řídit útratu pro Azure i pro AWS. Tento článek vysvětluje, jak nastavit integraci a nakonfigurovat ji, aby bylo možné používat funkce Azure Cost Management k analýze nákladů a kontrole rozpočtů.

Služba Cost Management zpracuje sestavu nákladů a využití AWS uloženou v kbelíku S3 pomocí přihlašovacích údajů pro přístup k AWS, aby získala definice sestav a stáhla soubory sestav GZIP CSV.

Další informace o nastavení integrace sestav AWS najdete ve videu věnovaném [nastavení konektorů pro AWS ve službě Cost Management](https://www.youtube.com/watch?v=Jg5KC1cx5cA). Další videa najdete v [kanálu služby Cost Management na YouTube](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/Jg5KC1cx5cA]

## <a name="create-a-cost-and-usage-report-in-aws"></a>Vytvoření sestavy nákladů a využití v AWS

AWS doporučuje použít sestavu nákladů a využití ke shromáždění a zpracování nákladů AWS. Další informace najdete v dokumentaci [Sestava nákladů a využití AWS](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html).

Pomocí stránky **Cost & Usage Reports** (Sestavy nákladů a využití) v konzole Billing and Cost Management (Fakturace a správa nákladů) v AWS můžete vytvořit sestavu nákladů a využití podle následujících kroků:

1. Přihlaste se ke konzole pro správu AWS (AWS Management Console) a otevřete konzolu [Billing and Cost Management](https://console.aws.amazon.com/billing) (Fakturace a správa nákladů).
2. V navigačním podokně vyberte **Cost & Usage Reports** (Sestavy nákladů a využití).
3. Vyberte **Create Report** (Vytvořit sestavu).
4. Do pole **Report name** (Název sestavy) zadejte název sestavy.
5. V části **Additional report details** (Další podrobnosti sestavy) vyberte **Include resource IDs** (Zahrnout ID prostředků).
6. V části **Data refresh settings** (Nastavení aktualizace dat) vyberte, jestli chcete, aby se sestava nákladů a využití AWS aktualizovala, pokud AWS po dokončení faktury použije u vašeho účtu refundaci, kredity nebo poplatky za podporu. Při aktualizaci sestavy se do úložiště Amazon S3 nahraje nová sestava. Doporučujeme nechat toto nastavení vybrané.
7. Vyberte **Další**.
8. V části **S3 bucket** (Kbelík S3) vyberte **Configure** (Konfigurovat).
9. V dialogovém okně Configure S3 Bucket (Konfigurace kbelíku S3) zadejte název kbelíku a oblast, kde chcete vytvořit nový kbelík, a vyberte **Next** (Další).
10. Zaškrtněte políčko **I have confirmed that this policy is correct** (Potvrzuji, že jsou tyto zásady správné) a pak klikněte na **Save** (Uložit).
11. (Volitelné) V poli Report path prefix (Předpona cesty k sestavě) zadejte předponu cesty k sestavě, kterou chcete přidat k názvu sestavy.
Pokud nezadáte předponu, bude výchozí předponou název, který jste zadali pro sestavu. Rozsah kalendářních dat má formát `/report-name/date-range/`.
12. Jako **Time unit** (Časová jednotka) vyberte **Hourly** (Po hodině).
13. V části **Report versioning** (Správa verzí sestav) vyberte, zda chcete, aby každá verze sestavy přepsala předchozí verzi, nebo zda chcete další nové sestavy.
14. Možnost **Enable data integration for** (Povolit integraci dat pro) nevyžaduje žádný výběr.
15. Jako **Compression** (Komprese) vyberte **GZIP**.
16. Vyberte **Další**.
17. Po kontrole nastavení sestavy vyberte **Review and Complete** (Zkontrolovat a dokončit).

    Poznamenejte si název sestavy. Použijete ho v pozdějších krocích.

AWS může trvat až 24 hodin, než začne doručovat sestavy do vašeho kbelíku Amazon S3. Po zahájení doručování AWS aktualizuje alespoň jednou denně soubory sestav nákladů a využití AWS. Můžete pokračovat v konfiguraci prostředí AWS, aniž byste čekali na zahájení doručování.

## <a name="create-a-role-and-policy-in-aws"></a>Vytvoření role a zásady v AWS

Azure Cost Management přistupuje několikrát denně ke kbelíku S3, kde se nachází sestava nákladů a využití. Služba potřebuje přístup k přihlašovacím údajům, aby mohla kontrolovat nová data. Vytvořením role a zásady v AWS umožníte přístup služby Cost Management.

Pokud chcete povolit přístup účtu AWS na základě role ke službě Cost Management, role se vytvoří v konzole AWS. Musíte mít _název ARN role_ a _externí ID_ z konzoly AWS. Později je můžete použít na stránce **Vytvořit konektor AWS** ve službě Cost Management.

Použijte průvodce Vytvořit novou roli:

1. Přihlaste se ke konzole AWS a vyberte **Services** (Služby).
2. V seznamu služeb vyberte **IAM**.
3. Vyberte **Roles** (Role) a pak vyberte **Create Role** (Vytvořit roli).
4. Na další stránce vyberte **Another AWS account** (Jiný účet AWS).
5. Jako **Account ID** (ID účtu) zadejte **432263259397**.
6. V části **Options** (Možnosti) vyberte **Require external ID (Best practice when a third party will assume this role)** (Vyžadovat externí ID (doporučený postup, když bude tuto roli zastávat třetí strana)).
7. Jako **External ID** zadejte externí ID, což je sdílené heslo mezi rolí AWS a službou Azure Cost Management. Stejné externí ID se používá také na stránce **Nový konektor** ve službě Cost Management. Microsoft doporučuje, abyste při zadávání externího ID použili zásady pro silné heslo.
    > [!NOTE]
    > Neměňte výběr možnosti **Vyžadovat MFA**. Měla by zůstat nezaškrtnutá.
8. Vyberte **Další: Oprávnění**.
9. Vyberte **Vytvořit zásadu**. Otevře se nová karta prohlížeče. Tam vytvoříte zásadu.
10. Vyberte **Zvolte službu**.

Nakonfigurujte oprávnění pro sestavu nákladů a využití:

1. Zadejte **Sestava nákladů a využití**.
2. Vyberte **Úroveň přístupu** > **Čtení** > **DescribeReportDefinitions**. Tento krok umožňuje službě Cost Management přečíst, které sestavy CUR jsou definovány, a určit, jestli splňují požadavek na definici sestavy.
3. Vyberte **Přidat další oprávnění**.

Nakonfigurujte oprávnění pro vaše kbelíky a objekty S3:

1. Vyberte **Zvolte službu**.
2. Zadejte **S3**.
3. Vyberte **Úroveň přístupu** > **Seznam** > **ListBucket**. Tato akce načte seznam objektů ve kbelíku S3.
4. Vyberte **Úroveň přístupu** > **Číst** > **GetObject**. Tato akce povolí stažení fakturačních souborů.
5. Vyberte **Prostředky**.
6. Vyberte **kbelík – Přidat ARN**.
7. Do pole **Název kbelíku** zadejte kbelík, ve kterém se mají ukládat soubory CUR.
8. Vyberte **objekt – Přidat ARN**.
9. Do pole **Název kbelíku** zadejte kbelík, ve kterém se mají ukládat soubory CUR.
10. V poli **Název objektu** vyberte **Libovolný**.
11. Vyberte **Přidat další oprávnění**.

Nakonfigurujte oprávnění pro Průzkumníka nákladů:

1. Vyberte **Zvolte službu**.
2. Zadejte **Služba Průzkumník nákladů**.
3. Vyberte možnost **Všechny akce služby Průzkumník nákladů (ce:\*)** . Tato akce ověří, zda je kolekce správná.
4. Vyberte **Přidat další oprávnění**.

Přidejte oprávnění pro službu AWS Organizations:

1. Zadejte **Organizations**.
2. Vyberte **Úroveň přístupu** > **Seznam** > **ListAccounts**. Tato akce získá názvy účtů.
3. Do pole **Kontrola zásady** zadejte název nové zásady. Zkontrolujte, zda jste zadali správné informace, a pak vyberte **Vytvořit zásadu**.
4. Vraťte se na předchozí kartu a aktualizujte webovou stránku v prohlížeči. Na panelu hledání vyhledejte svou novou zásadu.
5. Vyberte **Další: Kontrola**.
6. Zadejte název nové role. Zkontrolujte, zda jste zadali správné informace, a pak vyberte **Vytvořit roli**.

    Poznamenejte si název ARN role a externí ID použité v předchozích krocích při vytváření role. Později je použijete při nastavování konektoru Azure Cost Management.

JSON obsahující zásadu by měl vypadat přibližně jako v následujícím příkladu. Nahraďte parametr _bucketname_ názvem svého kbelíku S3.

```JSON
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
"organizations:ListAccounts",
             "ce:*",
             "cur:DescribeReportDefinitions"
            ],
            "Resource": "*"
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::bucketname",
                "arn:aws:s3:::bucketname/*"
            ]
        }
    ]
}
```

## <a name="set-up-a-new-connector-for-aws-in-azure"></a>Nastavení nového konektoru pro AWS v Azure

Pomocí následujících informací vytvoříte konektor AWS a začnete monitorovat náklady na AWS:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Přejděte na domovskou stránku Azure kliknutím na Položku **Domů** v nabídce vlevo (ikona „hamburgerové“ nabídky se 3 řádky).
3. Přejděte na **Nástroje** > **Cost Management** v dolní části stránky.
3. V části **Nastavení** vyberte **Konektory pro AWS**.  
4. Vyberte **+ Přidat** v horní části stránky a vytvořte konektor.  
    :::image type="content" source="./media/aws-integration-setup-configure/aws-connector.png" alt-text="Příklad znázorňující nastavení Konektory pro AWS" :::
1. Na stránce **Vytvořit konektor** do pole **Zobrazovaný název** zadejte název konektoru.  
    :::image type="content" source="./media/aws-integration-setup-configure/create-aws-connector01.png" alt-text="Příklad stránky pro vytvoření konektoru AWS" :::
1. Volitelně můžete vybrat výchozí skupinu pro správu. Budou se do ní ukládat všechny zjištěné propojené účty. Můžete ji nastavit později.
1. Pokud chcete zajistit nepřetržitý provoz, v části **Fakturace** nastavte **Automaticky prodloužit** na **Zapnuto**. Pokud vyberete tuto automatickou možnost, musíte vybrat předplatné pro fakturaci.
1. Jako **Název ARN role** zadejte hodnotu, kterou jste použili při nastavování role v AWS.
1. Jako **Externí ID** zadejte hodnotu, kterou jste použili při nastavování role v AWS.
1. Jako **Název sestavy** zadejte název, který jste vytvořili v AWS.
1. Vyberte **Další** a potom vyberte **Vytvořit**.

Může trvat několik hodin, než se objeví nové rozsahy AWS, konsolidovaný účet AWS, propojené účty AWS a jejich data nákladů.

Po vytvoření konektoru doporučujeme, abyste k němu přiřadili řízení přístupu. Uživatelům se přiřadí oprávnění k nově zjištěným rozsahům: Konsolidovaný účet AWS a propojené účty AWS. Uživatel, který vytváří konektor, je vlastníkem konektoru, konsolidovaného účtu a všech propojených účtů.

Přiřazením oprávnění konektoru uživatelům po zjišťování se nepřiřadí oprávnění existujícím rozsahům AWS. Místo toho jsou přiřazena oprávnění pouze novým propojeným účtům.

## <a name="take-additional-steps"></a>Provedení dalších kroků

- [Nastavte skupiny pro správu](../../governance/management-groups/overview.md#initial-setup-of-management-groups), pokud jste to ještě neudělali.
- Ověřte, že se do výběru rozsahu přidaly nové rozsahy. Vyberte **Aktualizovat** a zobrazte si nejnovější data.
- Na stránce **Cloudové konektory** vyberte svůj konektor a vyberte **Přejít na fakturační účet**, abyste mohli přiřadit propojený účet ke skupinám pro správu.

> [!NOTE]
> Skupiny pro správu se v současnosti nepodporují pro zákazníky se Smlouvou se zákazníkem Microsoftu (MCA). Zákazníci se smlouvou MCA si mohou vytvořit tento konektor a zobrazit data AWS. Zákazníci se smlouvou MCA si ale nemohou zobrazovat náklady na Azure a náklady na AWS společně v rámci jedné skupiny pro správu.

## <a name="manage-aws-connectors"></a>Správa konektorů AWS

Když vyberete konektor na stránce **Konektory pro AWS**, můžete provést tyto akce:

- Vybráním možnosti **Přejít na fakturační účet** zobrazíte informace o konsolidovaném účtu AWS.
- Vybráním služby **Access Control** můžete spravovat přiřazení role pro konektor.
- Vybráním možnosti **Upravit** můžete aktualizovat konektor. Číslo účtu AWS nemůžete změnit, protože se zobrazuje v názvu ARN role. Můžete ale vytvořit nový konektor.
- Vybráním možnosti **Ověřit** znovu spustíte ověřovací test, abyste se ujistili, že služba Cost Management smí shromažďovat data pomocí nastavení konektoru.

:::image type="content" source="./media/aws-integration-setup-configure/aws-connector-details.png" alt-text="Příklad podrobností konektoru AWS" :::

## <a name="set-up-azure-management-groups"></a>Nastavení skupin pro správu Azure

Vložením svého předplatného Azure a propojených účtů AWS do stejné skupiny pro správu vytvoříte jedno místo, kde uvidíte informace o poskytovateli z různých cloudů. Pokud jste prostředí Azure ještě nenakonfigurovali pomocí skupin pro správu, přečtěte si téma [Počáteční nastavení skupin pro správu](../../governance/management-groups/overview.md#initial-setup-of-management-groups).

Pokud chcete rozdělit náklady, můžete vytvořit skupinu pro správu, která obsahuje jenom propojené účty AWS.

## <a name="set-up-an-aws-consolidated-account"></a>Nastavení konsolidovaného účtu AWS

Konsolidovaný účet AWS spojuje fakturaci a platby několika účtů AWS. Funguje taky jako propojený účet AWS. Podrobnosti o konsolidovaném účtu AWS můžete zobrazit pomocí odkazu na stránce konektoru AWS. 

:::image type="content" source="./media/aws-integration-setup-configure/aws-consolidated-account01.png" alt-text="Ukázka podrobností pro konsolidovaný účet AWS" :::

Na této stránce můžete provést následující:

- Vybráním možnosti **Aktualizovat** můžete hromadně aktualizovat přidružení propojených účtů AWS ke skupině pro správu.
- Vybráním služby **Access Control** můžete nastavit přiřazení role pro rozsah.

### <a name="permissions-for-an-aws-consolidated-account"></a>Oprávnění pro konsolidovaný účet AWS

Ve výchozím nastavení jsou oprávnění pro konsolidovaný účet AWS nastavena při vytvoření účtu na základě oprávnění konektoru AWS. Vlastníkem je autor konektoru.

Úroveň přístupu můžete spravovat pomocí stránky **Úroveň přístupu** v konsolidovaném účtu AWS. Propojené účty AWS ale nedědí oprávnění ke konsolidovanému účtu AWS.

## <a name="set-up-an-aws-linked-account"></a>Nastavení propojeného účtu AWS

Propojený účet AWS je místo, kde se vytvářejí a spravují prostředky AWS. Propojený účet funguje také jako hranice zabezpečení.

Na této stránce můžete provést následující:

- Vybráním možnosti **Aktualizovat** můžete aktualizovat přidružení propojeného účtu AWS ke skupině pro správu.
- Vybráním služby **Access Control** můžete nastavit přiřazení role pro rozsah.

:::image type="content" source="./media/aws-integration-setup-configure/aws-linked-account01.png" alt-text="Příklad stránky propojeného účtu AWS" :::

### <a name="permissions-for-an-aws-linked-account"></a>Oprávnění pro propojený účet AWS

Ve výchozím nastavení jsou oprávnění pro propojený účet AWS nastavena při vytvoření na základě oprávnění konektoru AWS. Vlastníkem je autor konektoru. Úroveň přístupu můžete spravovat pomocí stránky **Úroveň přístupu** v propojeném účtu AWS. Propojené účty AWS nedědí oprávnění z konsolidovaného účtu AWS.

Propojené účty AWS vždy dědí oprávnění ze skupiny pro správu, do které patří.

## <a name="next-steps"></a>Další kroky

- Teď, když jste nastavili a nakonfigurovali integraci sestavy nákladů a využití AWS, můžete pokračovat do části [Správa nákladů a využití AWS](aws-integration-manage.md).
- Pokud nejste obeznámeni s analýzou nákladů, přečtěte si úvodní příručku [Prozkoumání a analýza nákladů pomocí služby Analýza nákladů](quick-acm-cost-analysis.md).
- Pokud nejste obeznámeni s rozpočty v Azure, přečtěte si téma [Vytváření a správa rozpočtů Azure](tutorial-acm-create-budgets.md).
