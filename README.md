# pi2
package br.upis.sel.model.entity;

import java.util.Collection;
import java.util.List;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.EnumType;
import javax.persistence.Enumerated;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.JoinTable;
import javax.persistence.JoinColumn;
import javax.persistence.ManyToMany;
import javax.persistence.Table;
import javax.persistence.Transient;

import org.hibernate.annotations.LazyCollection;
import org.hibernate.annotations.LazyCollectionOption;
import org.hibernate.validator.constraints.br.CPF;
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.userdetails.UserDetails;

import br.upis.sel.enums.LoteStatus;

@Entity
@Table(name="tb_lote")
public class lote implements UserDetails {

	private static final long serialVersionUID = -558847129204545L;
	
	public lote() {
	}
	
	public lote(String id,
			LoteStatus status) {
		this.id = id;
		
	}

	@Id
	@GeneratedValue(strategy=GenerationType.AUTO)
	@Column(name = "lote_id")
	private Long idLote;
	
	@Column(name = "lote_valor")
	private String valor;
	
	@Column(name = "lote_status")
	@Enumerated(EnumType.STRING)
	private LoteStatus status;

	public Long getIdLote() {
		return idLote;
	}

	public void setIdLote(Long idLote) {
		this.idLote = idLote;
	}

	

	public LoteStatus getStatus() {
		return status;
	}

	public void setStatus(LoteStatus status) {
		this.status = status;
	}
	
	

	@Override
	@Transient
	public boolean isAccountNonExpired() {
		return this.status.equals(LoteStatus.ATIVO);
	}

	@Override
	@Transient
	public boolean isAccountNonLocked() {
		return this.status.equals(LoteStatus.ATIVO);
	}

	@Override
	@Transient
	public boolean isCredentialsNonExpired() {
		return this.status.equals(LoteStatus.ATIVO);
	}

	@Override
	@Transient
	public boolean isEnabled() {
		return this.status.equals(LoteStatus.ATIVO);
	}

	@Override
	public int hashCode() {
		final int prime = 31;
		int result = 1;
		result = prime * result
				+ ((idLote == null) ? 0 : idLote.hashCode());
		return result;
	}

	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (!(obj instanceof Lote))
			return false;
		Lote other = (Lote) obj;
		if (idLote == null) {
			if (other.idLote != null)
				return false;
		} else if (!idLote.equals(other.idLote))
			return false;
		return true;
	}
	
}
package br.upis.sel.model.dao;

import java.util.List;

import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.CrudRepository;
import org.springframework.data.repository.query.Param;

import br.upis.sel.model.entity.Lote;
import br.upis.sel.model.entity.Perfil;


public interface LoteDAO extends CrudRepository<Lote, Long> {
	
	Lote findById(String id);

	Lote findByStatus(String status);
	
}
package br.upis.sel.view.mb;

//import javax.faces.bean.ManagedBean;

import org.springframework.context.annotation.Scope;
import org.springframework.security.core.context.SecurityContextHolder;
import org.springframework.stereotype.Component;

import br.upis.sel.model.entity.Lote;

@Component
@Scope("session")
//@ManagedBean
public class LoteSession {
	
	private Lote lote;

	public Lote getLote() {
		this.lote = (Lote) SecurityContextHolder.getContext().getAuthentication().getPrincipal();
		return lote;
	}

	public void setLote(Lote lote) {
		this.lote = lote;
	}


package br.upis.sel.view.mb;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;


//import javax.faces.bean.ManagedBean;
import javax.faces.context.FacesContext;
import javax.servlet.http.HttpServletRequest;

import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Component;

import br.upis.sel.enums.LoteStatus;
import br.upis.sel.model.entity.Lote;


@Component
@Scope("view")
//@ManagedBean
public class ManterLoteMB extends AbstractMB {
	
	private static final long serialVersionUID = 7859756109388467543L;
	
	private String pesquisaID;
	private String pesquisaStatus;
	
//	@Autowired
	private Lote lote;
	
	private List<Lote> listaLote = null;
	
	
	private boolean renderizaCampoId;
	private boolean desabilitarAjaxCheckbox;
	private boolean renderizaBotaoReativar;
	
	
	public ManterLoteMB() {
		this.lote= new Lote();
	}

	public String getPesquisaId() {
		return pesquisaId;
	}

	public void setPesquisaId(String pesquisaId) {
		this.pesquisaId = pesquisaId;
	}


	
	public Participante getLote() {
		return lote;
	}

	public void setLote(Lote lote) {
		this.lote = lote;
	}

	public boolean isRenderizaBotaoReativar() {
		return renderizaBotaoReativar;
	}

	public void setRenderizaBotaoReativar(boolean renderizaBotaoReativar) {
		this.renderizaBotaoReativar = renderizaBotaoReativar;
	}


	public List<Lote> getListaLotes() {
		if (listaLotes == null) {
			listaLotes = this.facade.recuperarTodosLotes();
		}
		return listaLotes;
	}

	public void setListaLotes(List<Lote> listaLotes) {
		this.listaLotes = listaLotes;
	}



	public boolean isRenderizaCampoId() {
		return renderizaCampoId;
	}

	public void setRenderizaCampoId(boolean renderizaCampoId) {
		this.renderizaCampoId = renderizaCampoId;
	}

	public boolean isDesabilitarAjaxCheckbox() {
		if (this.Lote.getIdLote() != null) {
			desabilitarAjaxCheckbox = true;
		}
		return desabilitarAjaxCheckbox;
	}

	public void setDesabilitarAjaxCheckbox(boolean desabilitarAjaxCheckbox) {
		this.desabilitarAjaxCheckbox = desabilitarAjaxCheckbox;
	}

	public void zerarLote() {
		System.out.println(lote.getNome());
		this.lote = new Lote();
		this.perfisSelecionados = null;
	}
	
	public void salvarOuAlterarLote() {
		try {
			if (this.lote.getIdLote() == null) {
			
				this.facade.incluirLote(this.lote);
				this.getMessage("Lote adicionado com sucesso");
			} else {
				this.lote.getPerfis().clear();
				this.facade.alterarLote(this.lote, LoteStatus.ATIVO);
				this.getMessage("Lote alterado com sucesso");
			}
			this.listaLotes = null;
		} catch (Exception e) {
			e.printStackTrace();
			this.getError("Erro ao adicionar ou alterar lote");
		}
	}
	
	public void desativarLote() {
		try {
			this.facade.alterarLote(this.lote, LoteStatus.INATIVO);
			this.getMessage("Lote desativado com sucesso");
			this.listaLotes= null;
		} catch (Exception e) {
			e.printStackTrace();
			this.getError("Erro ao desativar lote");
		}
	}
	
	public void prepararAlteracao() {
		this.perfisSelecionados = this.lote.getPerfis().toArray(new Perfil[this.lote.getPerfis().size()]);	
	}
	

	
	@Override
	public void recuperarObjeto() {
		HttpServletRequest request = (HttpServletRequest) FacesContext.getCurrentInstance().getExternalContext().getRequest();
		String param = request.getParameter("loteId");
		Long loteId = Long.parseLong(param);
		this.lote = this.facade.recuperarLotePorId(loteId);
	}
}
package br.upis.sel.controller.bo.impl;

import java.util.ArrayList;
import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.authentication.encoding.ShaPasswordEncoder;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import br.upis.sel.controller.ManterLoteBO;
import br.upis.sel.enums.LoteStatus;
import br.upis.sel.enums.LoteDescricao;
import br.upis.sel.model.dao.LoteDAO;
import br.upis.sel.model.entity.Lote;


import com.google.common.collect.Lists;

@Service
@Transactional
public class ManterLoteBOImpl implements ManterLoteBO {

	@Autowired
	private LoteDAO loteDAO;




	@Override
	public List<Lote> buscarTodosLotes() {
		return Lists.newArrayList(this.loteDAO.findAll());
	}

	@Override
	public Lote buscarPorId(Long id) {
		return this.loteDAO.findOne(id);
	}

	

		if (p.getStatus() == null) {
			p.setStatus(LoteStatus.ATIVO);
		}
		
		



	@Override
	public void alterarLote(Lote l, LoteStatus ls) {
		
		Lote lote = this.loteDAO.findOne(p.getIdLote());
		lote.setId(l.getSId());
		lote.setStatus(pls);
		
		
		
		
		this.incluirLote(lote);
	}
	
package br.upis.sel.controller.facade;

import java.io.Serializable;
import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import br.upis.sel.controller.ManterLoteBO;
import br.upis.sel.enums.LoteStatus;
import br.upis.sel.model.entity.Lote;


@Service
@Transactional
public class SELFacade implements Serializable {
	
	private static final long serialVersionUID = -6701772516498933248L;

	
	@Autowired
	private ManterLoteBO manterLoteBO;
	
	
	public List<Lote> recuperarLotePorId(Id id) {
		return this.manterLoteBO.buscarPorId(id);
	}
	
	public List<Lote> recuperarTodosLotes() {
		return this.manterLoteBO.buscarTodosLotes();
	}
	
	public List<Id> recuperarTodosId() {
		return this.perfilBO.buscarTodosId();
	}
	
	public Lote recuperarLotePorId(Long id) {
		return this.manterLoteBO.buscarPorId(id);
	}
	
	public void incluirLote(Lote l) {
		this.manterLoteBO.incluirLote(l);
	}
	
	public void alterarLote(Lote l, LoteStatus ls) {
		this.manterLoteBO.alterarLote(l, ls);
	}
}
/**
 * 
 */
package br.upis.sel.model.entity;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.Table;

/**
 * @author THIAGO
 *
 */
@Entity
@Table(name = "tb_lote")
public class Lote {
	
	@Id
	@GeneratedValue(strategy = GenerationType.AUTO)
	@Column(name = "lote_id")
	private Long idLote;

	public Long getIdLote() {
		return idLote;
	}

	public void setIdLote(Long idLote) {
		this.idLote = idLote;
	}
}
