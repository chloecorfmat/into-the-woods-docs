# Contrôle d'accès

## Configuration de Symfony

### Rôles

Afin de gérer les contrôles d'accès aux différents parties de l'application 3 rôles ont été créés :

- **Super Admin** : Administrateur de l'application, il a accès aux outils d'administration, la gestion des comptes notamment
- **Organizer** : Organisateurs de raids
- **Collaborateur** : Collaborateur d'un organisateur, il a accès à la gestion d'un raid auquel il est invité mais ne peut pas en créer. Il n'a pas accès à la gestion des collaborateurs et récupère les types de POI du créateur du raid.
- **Helper** : Bénévoles

### security.yml

Ces rôles sont définis dans la partie `role_hierarchy` du fichier `app/config/security.yml`

Ce fichier comprend également la configuration des accès en fonction de l'url. On peut ainsi désigner les rôles qui ont accès aux différents partie de l'application web. Ainsi, seul les super administrateurs peuvent accèder aux adresse commençant par `/admin`avec la configuration suivante : `- { path: ^/admin, role: ROLE_SUPER_ADMIN }`


## *Voter* : gestion de l'accès aux ressources

Les contrôles d'accès basés sur le rôle de l'utilisateur permettent de restreindre l'accès à certaines parties de l'application mais ne suffisent pas pour limiter l'accès à certaines ressources.

Afin d'éviter qu'un organisateur puisse modifier un raid qui ne lui appartient pas il faut limiter l'accès à certaines pages aux propriétaires des ressources. C'est le rôle des *Voter* de Symfony.

Un *voter* permet de vérifier certaines informations puis de valider ou non l'accès à certaines ressources. 

Pour utiliser un *Voter* on utilise l'*authorization checker* de Symfony et on utilise la fonction *isGranted* en précisant le *Voter* et la ressource dont l'accès doit être vérifié. 

```php
$authChecker = $this->get('security.authorization_checker');
if (!$authChecker->isGranted(RaidVoter::EDIT, $raid)) {
    throw $this->createAccessDeniedException();
}
```

Dans le cas présenté ci-dessus on utilise le *RaidVoter* pour vérifier que l'utilisateur courant a bien les droits suffisants pour modifier un raid (ie. il est le créateur). SI ce n'est pas le cas on tente de le renvoyer sur la page précédente, sinon on lève une exception.

Ce *Voter* est placé dans le répertoire `OrganizerBundle/Security/RaidVoter.php`.