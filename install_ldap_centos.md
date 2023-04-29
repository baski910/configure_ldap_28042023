yum install openldap-client openldap-servers<br>
<br>
sudo systemctl start slapd<br>
<br>
firewall-cmd --add-service=ldap<br>
<br>
slappasswd<br>
<br>
create a file name ldpadomain.ldif<br>
dn: olcDatabase={1}monitor,cn=config<br>
changetype: modify<br>
replace: olcAccess<br>
olcAccess: {0}to * by dn.base="gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth"
nbspnbspread by dn.base="cn=Manager,dc=example,dc=com" read by * none<br>
<br>
dn: olcDatabase={2}hdb,cn=config<br>
changetype: modify<br>
replace: olcSuffix<br>
olcSuffix: dc=example,dc=com<br>
<br>
dn: olcDatabase={2}hdb,cn=config<br>
changetype: modify<br>
replace: olcRootDN<br>
olcRootDN: cn=Manager,dc=example,dc=com<br>
<br>
dn: olcDatabase={2}hdb,cn=config<br>
changetype: modify<br>
replace: olcRootPW<br>
olcRootPW: {SSHA}PASSWORD<br>
<br>
dn: olcDatabase={2}hdb,cn=config<br>
changetype: modify<br>
add: olcAccess<br>
olcAccess: {0}to dn.base=""<br>
&nbsp&nbspby * read<br>
olcAccess: {1}to *<br>
nbspnbspby dn="cn=Manager,dc=example,dc=com" write<br>
nbspnbspby * read<br>
<br>
<br>
execute the following command to add the ldapdomain.ldif to server<br>
ldapmodify -Y EXTERNAL -H ldapi:/// -f ldapdomain.ldif<br>
<br>
Execute the following commands to update ldap database<br>
<br>
cp /usr/share/openldap-servers/DB_CONFIG.example /var/lib/ldap/DB_CONFIG<br>
chown -R ldap:ldap /var/lib/ldap/DB_CONFIG<br>
systemctl restart slapd<br>
<br>
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/cosine.ldif<br>
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/nis.ldif<br>
ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/inetorgperson.ldif<br>
<br>
create a file with a name base.ldif with following contents<br>
<br>
dn: dc=example,dc=com<br>
dc: example<br>
objectClass: top<br>
objectClass: domain<br>
<br>
dn: cn=Manager,dc=example,dc=com<br>
objectClass: organizationalRole<br>
cn: Manager<br>
description: Directory Manager<br>
<br>
dn: ou=People,dc=example,dc=com<br>
objectClass: organizationalUnit<br>
ou: People<br>
<br>
dn: ou=Group,dc=example,dc=com<br>
objectClass: organizationalUnit<br>
ou: Group<br>
<br>
Execute the following command to add the above structure to ldap database<br>
ldapadd -x -W -D "cn=Manager,dc=example,dc=com" -f base.ldif<br>
<br>
<br>
create a file name user.ldif with following contents<br>
dn: uid=bob,ou=People,dc=example,dc=com<br>
objectClass: top<br>
objectClass: account<br>
objectClass: posixAccount<br>
objectClass: shadowAccount<br>
cn: bob<br>
uid: bob<br>
uidNumber: 1005<br>
gidNumber: 1005<br>
homeDirectory: /home/bob<br>
userPassword: {SSHA}PASSWORD<br>
loginShell: /bin/bash<br>
gecos: bob<br>
shadowLastChange: 0<br>
shadowMax: 0<br>
shadowWarning: 0<br>
<br>
Execute the following command to add the user to ldap database<br>
ldapadd -x -W -D "cn=Manager,dc=example,dc=com" -f user.ldif<br>
