# exercicio
Procuro codificar sempre repetindo uma mesma estruturação de codificação já existente,
seja em um objeto de banco ou tela. 
Se houver consultas,procuro respeitar a estrutura relacional.
Se há problemas de desempenho, antes de testar soluções, sei há configurações estabelecidas na adm. do banco para permitir que um hint seja executado, ou não. E sei tambem que uma  mesma consulta, não gera necessariamente, o mesmo plano de execução, em ambientes diferentes 

----------------------------------------------------------------------
Questão  2:
class Cls35{
public static void main(String[] args){
for(int i = 1; i <= 100; i++) {
if(i%3 == 0){
System.out.println("FOO ");
if(i%5 == 0){
System.out.println("BAA ");
if(i%3==0 && i%5==0)){ 
System.out.println("FooBaa");
    }
   }
  }
}

----------------------------------------------------------------------
Questão 5:
select
case
when N.nota < 8 THEN 'NULL' when N.nota >- 8 THEN A.nome
end
A.id, 
A.valor
from Nota
N Aluno A
where
N.nota = A.ID 
and
A. valor <= N.nmax
and
A.valor >= N.min
order by N.nota desc, A.nome asc;
----------------------------------------------------------------------
Questão 8:
select distinct name from 
(
select name from cats
union all
select name from dogs
)animals
----------------------------------------------------------------------
Questão 10:
Resposta : Será sempre 0
-----------------------------------------------------------------------
Questão 11e 12 :

--BLOCO ANONIMO
declare

begin 
  set serveroutput on; 
   pkg_equipe_mgmt.prc_estrutura; 
   pkg_equipe_mgmt.prc_IncluiEquipe('ALPHA1','MT_07019','13TRF','E08796',0); 
   pkg_equipe_mgmt.prc_IncluiEquipe('BETA2',' MT_11606','13TRF',' E08115',1);
   pkg_equipe_mgmt.prc_IncluiEquipe('BETA1',' MT_07901', 1'3TRF',' E08115',1);
   commit;
   dbms_output.put_line('Processo processo equipe _mgmt executado com sucesso');
exception 
  when others then 
   rollback;
   raise_application_error(-20001,'Erro no processo equipe _mgmt - ' ||sqlcode|| ' -error- ' ||sqlerrm);
end ; 
-------------------------------------------------------------------------------------
--PACKAGE 
create package pkg_equipe_mgmt
as 
  procedure prc_estrutura;
  procedure  prc_IncluiEquipe(
      c_nome tb_equipe.nome%type, 
      c_nome1 tb_equipe.nome_b1%type, 
      c_nome2 tb_equipe.nome_b2%type, 
      c_nome3 tb_equipe.nome_b3%type, 
      c_status tb_equipe.status%type);   
  function fn_atualiza_tarefa(p_nome in varchar2,p_tarefa in varchar2) return varchar2;
end pkg_equipe_mgmt;  
-------------------------------------------------------------------------------------
--PACKAGE BODY
create or replace  packge body  pkg_equipe_mgmt 
as 
     procedure  prc_IncluiEquipe(c_oid  tb_equipe.oid%type, 
      c_nome tb_equipe.nome%type, 
      c_nome1 tb_equipe.nome_b1%type, 
      c_nome2 tb_equipe.nome_b2%type, 
      c_nome3 tb_equipe.nome_b3%type, 
      c_status tb_equipe.status%type) 
   is 
   begin
    insert into tb_equipe (nome,nome_b1,nome_b2,nome_b3,status)
         values(c_nome, c_nome1, c_nome2, c_nome3, c_status ); 
   end  prc_IncluiEquipe; 
   
procedure prc_estrutura
as 
create sequence sqEquipe
    MINVALUE 1
    MAXVALUE 300
    START WITH 1
    INCREMENT BY 1
    CACHE 20;
 create table tb_equipe(
    oid NUMBER ,
    nome VARCHAR2(50) ,
    nome_b1 VARCHAR2(50) ,
    nome_b2 VARCHAR2(50) ,
    nome_b3 VARCHAR2(50) ,
    status number(1)
);
 create table  tb_equipe_tarefa(
    oid NUMBER ,
    nome VARCHAR2(50) ,
    data_criacao date ,
    area VARCHAR2(100) ,
    equipe_responsavel number
);
  create table  tb_equipe_log(
    oid NUMBER ,
    data date ,
    codigo VARCHAR2(50) ,
    descricao VARCHAR2(100) ,
    equipe_responsavel number
);
end prc_estrutura;

function fn_atualiza_tarefa(p_nome in varchar2,p_tarefa in varchar2)     
  return   number is
   nr_sequencial                    tb_equipe.oid%type,
   v_equipe_responsavel      tb_equipe_tarefa. equipe_responsavel%type,
   v_area                              varchar2(150);
   v_oid                                 tb_equipe.oid%type,
   v_msg_ret                          char;
   v_status                             tb_equipe.status%type,
   v_data                                tb_equipe.data_criacao%type,
begin
   v_oid := 0;
   v_msg_ret  := 0;
   v_status :=  0;
   v_data := to_date(trunc(sysdate));
   select sqEquipe.nextval into nr_sequencial  from dual;
   
   select nvl(nome_b1,'')||'/'||nvl(nome_b2,'')||'/'||nvl(nome_b3,'') into v_area from tb_equipe;
   
   select 
    e.oid, e.status into v_oid,v_status 
   from tb_equipe e
   where e.nome <> p_nome
   and exists 
   (select 1 from tb_equipe q
     where q.nome_b1 = e.nome_b1
     and     q.nome_b2 = e.nome_b2
     and     q.nome_b3 = e.nome_b3
     and     q.nome = p_nome );
     
   -- caso exista a equipe 
   if  nvl(v_oid, 0) <> 0 then
       v_equipe_responsave := v_oid;
       v_msg_ret  := 0;
   end if ;
   
    -- caso não exista a equipe
   if  nvl(v_oid, 0) = 0 then
       v_equipe_responsavel := 0;
       v_msg_ret  := -1;
   end if ;
   -- caso exista a equipe mas esta inativa
   if nvl(v_status, 0) = 0 then
       v_equipe_responsave := 0;
       v_msg_ret  := -2;
   end if;
   
   update  tb_equipe set oid = nr_sequencial where nome = pnome ;
   
   insert into tb_equipe_tarefa (oid,nome,data_criacao,area,equipe_responsavel)values (nr_sequencial, p_nome,v_data,v_area,v_equipe_responsavel)
   
   insert into tb_equipe_log(oid,data,codigo,descricao,equipe_responsavel)values (nr_sequencial, v_data,'codigo','descrição',v_equipe_responsavel )
    return 0 ;
    

when no_data_found then 
v_msg_ret  := -2;
   insert into tb_equipe_tarefa (oid,nome,data_criacao,area,equipe_responsavel)values (nr_sequencial, p_nome,v_data,v_area,      v_equipe_responsavel )
   insert into tb_equipe_log(oid,data,codigo,descricao,equipe_responsavel)values (nr_sequencial, v_data )
    return 0 ;

 when others then 
   raise_application_error(-20001,'Erro no processo equipe _mgmt - '||SQLCODE||' -ERROR- '||SQLERRM);
end fn_atualiza_tarefa;

end pkg_equipe_mgmt;


