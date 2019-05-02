---
title: Nastavení a konfigurace AWS nákladů a využití integrace sestavy ve službě Azure Cost Management
description: Tento článek vás provede, když nastavení a konfigurace AWS nákladů a využití integrace sestavy ve službě Azure Cost Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 688bcc02b14d101008afc76662fd6548446cb329
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870279"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>Nastavení a konfigurace AWS nákladů a využití integrace sestavy

Díky integraci Amazon Web Services nákladů a využití sestav můžete monitorovat a kontrolovat útraty AWS ve službě Azure Cost Management. Integrace umožňuje na jednom místě na webu Azure portal, kde můžete monitorovat a ovládací prvek výdaje za Azure a AWS. Tento článek vysvětluje, jak nastavení integrace a nakonfigurujte ho tak, aby analýza nákladů a zkontrolovat rozpočty použijete funkce Cost Management.

Služba Cost Management přečte AWS nákladů a využití sestavy, které jsou uložené v sady S3 pomocí vašich přihlašovacích údajů AWS přístup k získání definice sestav a stáhnout sestavu, kterou souborů GZIP sdíleného svazku clusteru.

## <a name="create-a-cost-and-usage-report-in-aws"></a>Vytvoření sestavy nákladů a využití v AWS

Pomocí sestavy nákladů a využití je AWS doporučuje způsob, jak shromažďovat a zpracovávat náklady na AWS. Další informace najdete v tématu [náklady na AWS a sestava využití](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html) článek dokumentace.

Použití **sestavy** stránka konzoly správou nákladů a fakturací v AWS, chcete-li vytvořit sestavy nákladů a využití pomocí následujících kroků.

1. Přihlaste se do konzoly pro správu AWS a správou nákladů a fakturací na otevřete konzolu https://console.aws.amazon.com/billing.
2. V navigačním podokně klikněte na tlačítko **sestavy**.
3. Klikněte na tlačítko **vytvoření sestavy**.
4. Pro **název sestavy**, zadejte název pro sestavu.
5. Pro **časovou jednotku**, zvolte **hodinové**.
6. Pro **zahrnout**, přidejte ID každého prostředku v sestavě a vyberte **ID prostředků**.
7. Pro **povolit podporu pro**, není nutný žádný výběr.
8. Pro **nastavení aktualizace dat**vyberte **automaticky aktualizovat vaše náklady &amp; využití sestav při zjištění poplatky za předchozí měsíce s uzavřeno faktury**.
9. Klikněte na **Další**.
10. Pro **sadu Amazon S3**, zadejte název sady Amazon S3, kam chcete předpovědí a klikněte na tlačítko **ověřte**. Kontejner musí mít příslušná oprávnění platná. Další informace o přidání oprávnění do kontejneru, naleznete v tématu [nastavení kontejneru a objektu přístupová oprávnění](http://docs.aws.amazon.com/AmazonS3/latest/user-guide/set-permissions.html).
11. Pro **předpona cesty sestavy**, zadejte předponu cesta sestavy, které chcete přidat k názvu sestavy.
12. Pro **komprese**vyberte **GZIP**.
13. Klikněte na **Další**.
14. Po zkontrolování nastavení sestavy, klikněte na tlačítko **zkontrolujte a dokončete**.
    Poznámka: **název sestavy**. Použijete ho v dalších krocích.

Může trvat až 24 hodin AWS ke spuštění doručování sestavy do vašeho kontejneru Amazon S3. Po spuštění doručování AWS aktualizuje soubory sestav AWS nákladů a využití alespoň jednou za den.

## <a name="create-a-role-and-policy-in-aws"></a>Vytvoření role a zásad AWS

Azure Cost Management zajišťuje přístup ke kontejneru S3, kde nákladů a využití je sestava umístěna několikrát za den. Služba Cost Management potřebuje přístup k přihlašovacím údajům a kontrolují dostupnost nových dat. Vytvoření Role a zásad v AWS, pokud chcete povolit přístup služba Cost Management.

Pokud chcete povolit přístup na základě rolí k účtu AWS ve službě Azure Cost Management, je role vytvořené v konzole AWS. Je potřeba mít _Role ARN_ a _externí ID_ v konzole AWS. Později je použijete v části Vytvoření konektoru stránku AWS ve službě Azure Cost Management.

1. Přihlaste se ke konzole AWS a vyberte **služby**.
2. V seznamu služeb vyberte **IAM**.
3. Vyberte **role** a potom klikněte na tlačítko **vytvořit roli**.
4. Na další stránce vyberte **účtu AWS jiného**.
5. V **ID účtu** , zadejte _432263259397_.
6. V **možnosti**vyberte **vyžadují externí ID (doporučený postup při externí bude předpokládat této role)**.
7. V **externí ID**, zadejte externí ID. Externí ID se sdílený přístupový kód mezi AWS Role a Azure Cost Management. Na stránce Nový konektor ve službě Cost Management je také použít stejné ID. externí. Například vypadá podobně jako externí ID _Companyname1234567890123_.
    Neměňte výběr **vyžadovat vícefaktorové ověřování**. By měla zůstat prázdné.
8. Klikněte na tlačítko **Další: Oprávnění**.
9. Klikněte na tlačítko **vytvořit zásadu**. Na nové kartě prohlížeče se otevře, ve kterém vytvoříte novou zásadu.
10. Klikněte na tlačítko **vybrat službu**.
11. Typ **náklady a sestava využití**.
12. Vyberte **úroveň přístupu**, **čtení** > **DescribeReportDefinitions**. Díky tomu, že náklady na správu, přečtěte si, co stejné zprávy jsou definovány a určit, pokud se shodují kontrolu požadovaných součástí definice sestavy.
13. Klikněte na tlačítko **přidat další oprávnění**.
14. Klikněte na tlačítko **vybrat službu**.
15. Typ _S3_.
16. Vyberte **úroveň přístupu**, **seznamu** > **ListBucket**. Tato akce načte seznam objektů v intervalu S3.
17. Vyberte **úroveň přístupu**, **čtení** > **GetObject**. Tato akce umožňuje fakturace stahování souborů.
18. Vyberte **prostředky**.
19. Vyberte **kontejneru – přidání ARN**.
20. V **název sektoru**, zadejte do kbelíku používá k ukládání souborů MĚNA.
21. Vyberte **objekt – přidání ARN**.
22. V **název sektoru**, zadejte do kbelíku používá k ukládání souborů MĚNA.
23. V **název objektu**vyberte **jakékoli**.
24. Klikněte na tlačítko **přidat další oprávnění**.
25. Klikněte na tlačítko **vybrat službu**.
26. Typ _nákladů Průzkumníka služby_.
27. Vyberte **akce Explorer – všechny náklady na služby (ce:\*)**. Tato akce ověří správnost kolekce.
28. Klikněte na tlačítko **přidat další oprávnění**.
29. Typ **organizace**.
30. Vyberte **úroveň přístupu, seznam** > **Vypisovat účty**. Tato akce získá názvy účtů.
31. V **zásady revize**, zadejte název pro novou zásadu. Zkontrolujte, ujistěte se, že jste zadali správné informace a pak klikněte na tlačítko **vytvořit zásadu**.
32. Přejděte zpět na předchozí kartu a aktualizujte webovou stránku v prohlížeči. Na panelu hledání vyhledejte novou zásadu.
33. Vyberte **Další: Revize**.
34. Zadejte název pro novou roli. Zkontrolujte, ujistěte se, že jste zadali správné informace a pak klikněte na tlačítko **vytvořit roli**.
    Poznámka: **Role ARN** a **externí ID** v předchozích krocích jste použili při vytváření Role. Použijete je později při nastavování konektoru Azure Cost Management.

Zásady JSON by měl vypadat podobně jako v následujícím příkladu. Nahraďte _bucketname_ s názvem vaší sady S3.

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

## <a name="set-up-a-new-aws-connector-in-azure"></a>Vytvořit nový konektor AWS v Azure

Tyto informace můžete vytvořit nový konektor AWS a začít monitorovat vaše náklady na AWS.

1. Přihlásit se k webu Azure portal na https://portal.azure.com.
2. Přejděte do **Cost Management a fakturace** > **Cost Management**.
3. V části **nastavení**, klikněte na tlačítko **konektory (Preview) v cloudu**.  
    ![Příklad zobrazující konektory Cloud (nastavení ve verzi Preview)](./media/aws-integration-setup-configure/cloud-connectors-preview01.png).
4. Klikněte na tlačítko **+ přidat** v horní části stránky a vytvořte nový konektor.
5. Na stránce vytvořit stránku konektor AWS, zadejte **zobrazovaný název** název vašeho konektoru.  
    ![Příklad vytvoření stránku konektor AWS](./media/aws-integration-setup-configure/create-aws-connector01.png)
6. Volitelně vyberte výchozí skupiny pro správu. Uloží všechny zjištěné propojené účty. Můžete nastavit ji později.
7. V části **fakturace** vyberte **automaticky účtovat 1 % ve fázi obecné dostupnosti** Pokud chcete zajistit nepřetržité provádění po vypršení platnosti verze preview. Pokud vyberete automatickou možnost, musíte vybrat fakturaci **předplatné**.
8. Zadejte **Role ARN**. Jedná se o hodnotu, která jste použili při nastavování role v AWS.
9. Zadejte **externí ID**. Jedná se o hodnotu, která jste použili při nastavování role v AWS.
10. Zadejte **název sestavy** , kterou jste vytvořili v AWS.
11. Klikněte na tlačítko **Další** a potom klikněte na tlačítko **vytvořit**.

Může trvat několik hodin pro nové obory AWS, konsolidovat účtu AWS a propojené účty AWS a jejich nákladech se zobrazí.

Po vytvoření konektoru, doporučujeme přiřadit řízení přístupu ke konektoru. Uživatelé jsou přiřazení oprávnění pro nově zjištěné obory: AWS konsolidovat účtu a AWS propojené účty. Uživatel, který vytvoří konektor je vlastníkem konektor, konsolidované účet a všechny propojené účty.

Přiřazení oprávnění konektorů uživatelům, když dojde k zjišťování nebude přiřadit oprávnění ke stávající obory AWS. Místo toho jsou pouze nové propojené účty přiřadit oprávnění.

## <a name="additional-steps"></a>Další kroky

- [Nastavení skupin pro správu](../governance/management-groups/index.md#initial-setup-of-management-groups), pokud jste tak již neučinili.
- Zkontrolujte, že nové obory se přidají do vašeho výběr oboru. Nezapomeňte kliknout na **aktualizovat** zobrazíte nejnovější data.
- Na stránce konektoru cloudu vyberte váš konektor a kliknutím na **přejít na fakturační účet** propojeného účtu přiřadit ke skupinám pro správu.

## <a name="manage-cloud-connectors"></a>Správa cloudových konektorů

Když vyberete konektor na stránce konektory Cloud, můžete:

- Klikněte na tlačítko **přejděte do fakturačního účtu** zobrazíte informace o účtu AWS konsolidovat.
- Klikněte na tlačítko **řízení přístupu** Spravovat přiřazení rolí konektoru.
- Klikněte na tlačítko **upravit** k aktualizaci konektoru. Číslo účtu AWS, nelze změnit, jak se zobrazí v Role ARN. Můžete však vytvořit nový konektor.
- Klikněte na tlačítko **ověřte** znovu spustit ověřovací test, abyste měli jistotu, že Cost Management může shromažďovat data pomocí nastavení konektoru.

![Příklad zobrazující seznam konektorů, vytvořený AWS](./media/aws-integration-setup-configure/list-aws-connectors.png)

## <a name="role-of-azure-management-groups"></a>Role skupin pro správu Azure

Chcete-li vytvořit na jednom místě, chcete-li zobrazit informace o poskytovateli cloudu, musíte umístit vaše předplatná Azure a AWS, propojené účty ve stejné skupině pro správu. Pokud už jste nenakonfigurovali prostředí Azure pomocí skupin pro správu, přečtěte si téma [počáteční nastavení skupin pro správu](../governance/management-groups/index.md#initial-setup-of-management-groups).

Pokud chcete oddělit náklady, můžete vytvořit skupinu pro správu, který obsahuje pouze AWS, propojené účty.

## <a name="aws-consolidated-account"></a>Konsolidované AWS účtu

Konsolidované AWS účet se používá pro konsolidaci fakturace a plateb pro několik účtů AWS. Účtu AWS konsolidovat taky pracuje jako AWS propojený účet.

![Příklad podrobností konsolidovat účtu AWS](./media/aws-integration-setup-configure/aws-consolidated-account01.png)

Na stránce můžete:

- Klikněte na tlačítko **aktualizovat** k hromadné aktualizace AWS propojené účty přidružení skupiny pro správu.
- Klikněte na tlačítko **řízení přístupu** k nastavení oboru přiřazení role.

### <a name="aws-consolidated-account-permissions"></a>Oprávnění účtu AWS konsolidováno

Ve výchozím nastavení konsolidovat AWS účet, který jsou oprávnění po jejich vytvoření na základě oprávnění konektoru AWS. Tvůrce konektoru je vlastníkem.

Úroveň přístupu můžete spravovat pomocí stránky úroveň přístupu účtu AWS konsolidovat. Nicméně oprávnění AWS konsolidovat účtu AWS propojené účty nejsou zděděny.

## <a name="aws-linked-account"></a>AWS propojený účet

AWS propojený účet je, kde se vytváří a spravují prostředky AWS. Propojený účet také slouží jako hranice zabezpečení.

Na této stránce můžete:

- Klikněte na tlačítko **aktualizovat** aktualizovat AWS propojená přidružení účtu se skupinou pro správu.
- Klikněte na tlačítko **řízení přístupu** k nastavení oboru přiřazení role.

![Příklad stránky propojeného účtu AWS](./media/aws-integration-setup-configure/aws-linked-account01.png)

### <a name="aws-linked-account-permissions"></a>AWS propojený účet oprávnění

Ve výchozím nastavení AWS propojené oprávnění účtu nastavené při vytvoření, na základě oprávnění konektoru AWS. Tvůrce konektoru je vlastníkem. Úroveň přístupu můžete spravovat pomocí stránky úroveň přístupu účtu AWS propojené. Oprávnění účtu AWS konsolidovat nejsou zděděny AWS propojené účty.

AWS propojené účty vždy dědí oprávnění ze skupiny pro správu, do kterých patří.

## <a name="next-steps"></a>Další postup

- Teď, když jste nastavili a nakonfigurovali AWS nákladů a využití integrace sestavy, přejít k [AWS Správa nákladů a využití](aws-integration-manage.md).
- Pokud nejste obeznámeni s analýzy nákladů, přečtěte si téma [zkoumat a analyzovat náklady pomocí Cost analysis](quick-acm-cost-analysis.md) rychlý start.
- Pokud nejste obeznámeni s rozpočty v Azure, přečtěte si téma [vytvořit a spravovat Azure rozpočty](tutorial-acm-create-budgets.md) kurzu.
