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
  read by dn.base="cn=Manager,dc=example,dc=com" read by * none<br>
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
add: olcRootPW<br>
olcRootPW: {SSHA}PASSWORD<br>
<br>
dn: olcDatabase={2}hdb,cn=config<br>
changetype: modify<br>
add: olcAccess<br>
olcAccess: {0}to attrs=userPassword,shadowLastChange by
  dn="cn=Manager,dc=example,dc=com" write by anonymous auth by self write by * none<br>
olcAccess: {1}to dn.base="" by * read<br>
olcAccess: {2}to * by dn="cn=Manager,dc=example,dc=com" write by * read<br>


execute the following command to add the ldapdomain.ldif to server<br>
ldapmodify -Y EXTERNAL -H ldapi:/// -f ldapdomain.ldif<br>

