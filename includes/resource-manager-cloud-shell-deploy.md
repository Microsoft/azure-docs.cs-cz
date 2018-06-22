## <a name="deploy-template-from-cloud-shell"></a>Nasazení šablony ze služby Cloud Shell

K nasazení šablony můžete použít [Cloud Shell](../articles/cloud-shell/overview.md). Ale je nutné nejdřív načíst vaše šablona do účtu úložiště pro své cloudové prostředí. Pokud jste ještě službu Cloud Shell nepoužívali, přečtěte si téma [Přehled služby Azure Cloud Shell](../articles/cloud-shell/overview.md), kde najdete informace o jejím nastavení.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Vyberte vaši skupinu prostředků služby Cloud Shell. Vzor názvů je `cloud-shell-storage-<region>`.

   ![Výběr skupiny prostředků](./media/resource-manager-cloud-shell-deploy/select-cs-resource-group.png)

1. Vyberte účet úložiště pro službu Cloud Shell.

   ![Výběr účtu úložiště](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. Vyberte **objekty BLOB**.

   ![Vyberte objekty BLOB](./media/resource-manager-cloud-shell-deploy/select-blobs.png)

1. Vyberte **+ kontejner**.

   ![Přidat kontejner](./media/resource-manager-cloud-shell-deploy/add-container.png)

1. Název a úroveň přístupu, zadejte vaše kontejneru. Ukázka šablony v tomto článku obsahuje žádné citlivé informace, takže povolit anonymní přístup pro čtení. Vyberte **OK**.

   ![Zadejte hodnoty kontejneru](./media/resource-manager-cloud-shell-deploy/provide-container-values.png)

1. Vyberte kontejner, který jste vytvořili.

   ![Vyberte nový kontejner](./media/resource-manager-cloud-shell-deploy/select-container.png)

1. Vyberte **Nahrát**.

   ![Nahrát objekt blob](./media/resource-manager-cloud-shell-deploy/upload-blob.png)

1. Vyhledejte a nahrajte vaši šablonu.

   ![Nahrání souboru](./media/resource-manager-cloud-shell-deploy/find-and-upload-template.png)

1. Po odeslal, vyberte šablonu.

   ![Vyberte novou šablonu](./media/resource-manager-cloud-shell-deploy/select-new-template.png)

1. Zkopírujte adresu URL.

   ![Zkopírujte adresu URL](./media/resource-manager-cloud-shell-deploy/copy-url.png)

1. Otevřete příkazový řádek.

   ![Otevření služby Cloud Shell](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
