由于云技术和工具变得越来越容易使用，组织也使用越来越多的[软件即服务 (SaaS)](https://azure.microsoft.com/overview/what-is-saas/) 应用程序来提高生产力。 随着 SaaS 应用数目的增多，管理员管理帐户和访问权限也越来越困难，用户记住各种不同的密码也不是一件容易事。 单个管理这些应用程序需要额外的工作，也不太安全。

* 那些需要跟踪许多密码的雇员倾向于使用不太安全的方法来记住这些密码，或者是写下这些密码，或者是将相同的密码用于多个帐户。
* 新来某个雇员或者某个雇员离开时，其所有帐户都必须单独进行预配或取消预配。
* 另外，雇员可能会开始在不通过 IT 部门的情况下使用 SaaS 应用进行工作，也就是说，他们会在系统中创建自己的帐户，而这些帐户并没有经过 IT 管理员的批准，也不受其监视。  

解决所有这些难题的一个解决方案是单一登录 (SSO)。 它是管理多个应用并为用户提供一致登录体验的最简单方式。 Azure Active Directory (Azure AD) 提供强大的 SSO 解决方案和许多可用的预先集成的应用程序，此外还有教程，供管理员快速设置新应用并开始预配用户。

## <a name="how-does-azure-active-directory-integrate-apps"></a>Azure Active Directory 如何集成应用？
可以通过 Azure AD 集成应用和预配的帐户。 可以通过两种方法中的一种来实现此目的。

* 如果在应用库中预先集成了应用，则可通过该门户设置应用并配置启用 SSO 所需的设置。 不管使用什么库应用，用户在开始时都可以按照应用库和 Azure 门户中提供的分步说明启用单一登录。
* 即使应用不在库中，也可以在 Azure AD 中将大多数应用设置为自定义应用。 这需要的专业技术知识更多一些，否则无法进行配置。 你可以添加支持 SAML 2.0 的任何应用程序作为联合应用，或者添加具有 HTML 登录页的任何应用程序作为密码 SSO 应用。

如果用户针对 SaaS 应用自行创建了不受 IT 部门管理的帐户，则可通过 [Cloud App Discovery](../articles/active-directory/active-directory-cloudappdiscovery-whatis.md) 工具解决问题。 该工具通过监视 Web 流量来确定哪些应用整个组织都在使用，以及每个这样的应用有多少人在使用。 IT 部门可以根据此信息了解用户喜爱使用的应用，从而决定要集成到适用于 SSO 的 Azure AD 中的内容。  

将应用集成到 Azure AD 中时，可将用户的固有应用程序标识映射到相应的 Azure AD 标识。  



<!--HONumber=Nov16_HO3-->


