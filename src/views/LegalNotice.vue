<template>
    <div class="container__vue">
        <Header/>    
        <main class="container__vue--mentions">
            <div class="container">
                <h1>MENTIONS LÉGALES</h1>
                <h2>1 - Édition du site</h2>
                <p>En vertu de l'article 6 de la loi n° 2004-575 du 21 juin 2004 pour la confiance dans l'économie numérique, il est précisé aux utilisateurs du site internet https://lancelletimote.github.io/TimoteLancelle_7_27092021_frontend/ l'identité des différents intervenants dans le cadre de sa réalisation et de son suivi:</p>

                <p>Propriétaire du site : Paul DUPONT - Groupomania : SARL - Contact : support-reseau-social@groupomania.com 0785239641 - Adresse : 12 avenue arsset, 67100 STRASBOURG, France.</p>

                <p>Identification de l'entreprise : SARL Paul DUPONT - Groupomania : SARL au capital social de 10000€ - SIREN : - RCS ou RM : - Adresse postale : 12 avenue arsset, 67100 STRASBOURG, France.</p>

                <p>Directeur de la publication : Paul DUPONT - Contact : paul-dupont@groupomania.com.</p>

                <p>Hébergeur : AutreGitHub 88 Colin P Kelly Jr Street San Francisco, CA 94107 United States 1-415-448-6673</p>

                <p>Délégué à la protection des données : Paul DUPONT - paul-dupont@groupomania.com</p>

                <p>Autres contributeurs :</p>

                <h2>2 - Propriété intellectuelle et contrefaçons</h2>
                <p>Paul DUPONT - Groupomania : SARL est propriétaire des droits de propriété intellectuelle et détient les droits d’usage sur tous les éléments accessibles sur le site internet, notamment les textes, images, graphismes, logos, vidéos, architecture, icônes et sons.</p>

                <p>Toute reproduction, représentation, modification, publication, adaptation de tout ou partie des éléments du site, quel que soit le moyen ou le procédé utilisé, est interdite, sauf autorisation écrite préalable de Paul DUPONT - Groupomania : SARL.</p>

                <p>Toute exploitation non autorisée du site ou de l’un quelconque des éléments qu’il contient sera considérée comme constitutive d’une contrefaçon et poursuivie conformément aux dispositions des articles L.335-2 et suivants du Code de Propriété Intellectuelle.</p>

                <h2>3 - Limitations de responsabilité</h2>
                <p>Paul DUPONT - Groupomania : SARL ne pourra être tenu pour responsable des dommages directs et indirects causés au matériel de l’utilisateur, lors de l’accès au site https://lancelletimote.github.io/TimoteLancelle_7_27092021_frontend/.</p>

                <p>Paul DUPONT - Groupomania : SARL décline toute responsabilité quant à l’utilisation qui pourrait être faite des informations et contenus présents sur https://lancelletimote.github.io/TimoteLancelle_7_27092021_frontend/.</p>

                <p>Paul DUPONT - Groupomania : SARL s’engage à sécuriser au mieux le site https://lancelletimote.github.io/TimoteLancelle_7_27092021_frontend/, cependant sa responsabilité ne pourra être mise en cause si des données indésirables sont importées et installées sur son site à son insu.</p>

                <p>Des espaces interactifs (espace contact ou commentaires) sont à la disposition des utilisateurs. Paul DUPONT - Groupomania : SARL se réserve le droit de supprimer, sans mise en demeure préalable, tout contenu déposé dans cet espace qui contreviendrait à la législation applicable en France, en particulier aux dispositions relatives à la protection des données.</p>

                <p>Le cas échéant, Paul DUPONT - Groupomania : SARL se réserve également la possibilité de mettre en cause la responsabilité civile et/ou pénale de l’utilisateur, notamment en cas de message à caractère raciste, injurieux, diffamant, ou pornographique, quel que soit le support utilisé (texte, photographie …).</p>

                <h2>4 - CNIL et gestion des données personnelles</h2>
                <p>Conformément aux dispositions de la loi 78-17 du 6 janvier 1978 modifiée, l’utilisateur du site https://lancelletimote.github.io/TimoteLancelle_7_27092021_frontend/ dispose d’un droit d’accès, de modification et de suppression des informations collectées. Pour exercer ce droit, envoyez un message à notre Délégué à la Protection des Données : Paul DUPONT - paul-dupont@groupomania.com.</p>

                <p>Pour plus d'informations sur la façon dont nous traitons vos données (type de données, finalité, destinataire...), lisez notre Politique de Confidentialité.</p>

                <p>Il est également possible de déposer une réclamation auprès de la CNIL.</p>

                <h2>5 - Liens hypertextes et cookies</h2>
                <p>Le site https://lancelletimote.github.io/TimoteLancelle_7_27092021_frontend/ contient des liens hypertextes vers d’autres sites et dégage toute responsabilité à propos de ces liens externes ou des liens créés par d’autres sites vers https://lancelletimote.github.io/TimoteLancelle_7_27092021_frontend/.</p>

                <p>La navigation sur le site https://lancelletimote.github.io/TimoteLancelle_7_27092021_frontend/ est susceptible de provoquer l’installation de cookie(s) sur l’ordinateur de l’utilisateur.</p>

                <p>Un "cookie" est un fichier de petite taille qui enregistre des informations relatives à la navigation d’un utilisateur sur un site. Les données ainsi obtenues permettent d'obtenir des mesures de fréquentation, par exemple.</p>

                <p>Vous avez la possibilité d’accepter ou de refuser les cookies en modifiant les paramètres de votre navigateur. Aucun cookie ne sera déposé sans votre consentement.</p>

                <p>Les cookies sont enregistrés pour une durée maximale de 1 mois.</p>

                <p>Pour plus d'informations sur la façon dont nous faisons usage des cookies, lisez notre Politique de Confidentialité.</p>

                <h2>6 - Droit applicable et attribution de juridiction.</h2>
                <p>Tout litige en relation avec l’utilisation du site https://lancelletimote.github.io/TimoteLancelle_7_27092021_frontend/ est soumis au droit français. En dehors des cas où la loi ne le permet pas, il est fait attribution exclusive de juridiction aux tribunaux compétents de ...</p>
            </div>
        </main>
        <Footer/>
    </div>
</template>

<script>
    import Header from '../components/Header.vue'
    import Footer from '../components/Footer.vue'

    export default {
        name: 'LegalNotice',
        components: {
            Header,
            Footer,
        },
    }
</script>

<style scoped lang="scss">
    h1, h2 {
        color: rgba(255, 255, 255, 1);
    }
    .container__vue {
        &--mentions {
            text-align: center;
            display: flex;
            justify-content: center;
            padding-bottom: 25px;
        }
    }
    @media (max-width: 576px) {
        h1 {
            font-size: 25px;
        }
        h2 {
            font-size: 20px;
        }
        p {
            font-size: 10px;
        }
    }
</style>