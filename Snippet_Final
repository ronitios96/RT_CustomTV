//
//  TblView+UIScrollView_Att1.swift
//  ZPOC
//
//  Created by Ronit on 06/07/24.
//

import Foundation
import UIKit
import SwiftUI

final class TestVC: UIViewController, MyTVDataSource {
    lazy var table: MyTV = {
        MyTV()
    }()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        table.frame = CGRect(x: 0, y: 0, width: view.frame.width, height: view.frame.height)
        table.autoresizingMask = [.flexibleWidth, .flexibleHeight]
        table.backgroundColor = .red
        self.view.addSubview(table)
        table.dataSource = self
        table.reloadData()
    }
    
    func numberOfRows() -> Int {
        return 11
    }
    
    func cellForRow(in tableView: MyTV, at indexPath: IndexPath) -> MyCell {
        let cell = tableView.dequeueReusableCell(at: indexPath)
        cell.label.text = "\(indexPath.row + 1)"
        return cell
    }
    
}

final class MyCell: UIView {
    
    lazy var label: UILabel = {
        let label = UILabel()
        label.textColor = .white
        label.font = UIFont.systemFont(ofSize: 12, weight: .heavy)
        label.textAlignment = .center
        return label
    }()
    
    override init(frame: CGRect) {
        super.init(frame: frame)
        self.backgroundColor = .gray
        self.addSubview(label)
        label.translatesAutoresizingMaskIntoConstraints = false
        NSLayoutConstraint.activate([
            label.centerXAnchor.constraint(equalTo: self.centerXAnchor),
            label.centerYAnchor.constraint(equalTo: self.centerYAnchor)
        ])
    }
    
    required init?(coder: NSCoder) {
        fatalError("Not using storyboards")
    }
    
    
}

protocol MyTVDataSource: AnyObject {
    func numberOfRows() -> Int
    func cellForRow(in tableView: MyTV, at indexPath: IndexPath) -> MyCell
}

final class MyTV: UIScrollView {
    var reusableCells: Set<MyCell> = .init()
    var visibleCells: [IndexPath: MyCell] = [:]
    private let rowHeight: CGFloat = 100
    
    weak var dataSource: MyTVDataSource?
    
    override func layoutSubviews() {
        super.layoutSubviews()
        guard let dataSource = self.dataSource else { return }
        setupVisibleCells(dataSource: dataSource)
    }
    
    func reloadData() {
        guard let dataSource = self.dataSource else { return }
        visibleCells.forEach({
            $0.value.removeFromSuperview()
        })
        visibleCells.removeAll()
        //Actual mechanism to setupVisible Cells
        setupVisibleCells(dataSource: dataSource)
        contentSize = .init(width: self.frame.width, height: CGFloat(dataSource.numberOfRows())*rowHeight)
        setNeedsLayout()
    }
    
    func setupVisibleCells(dataSource: MyTVDataSource) {
        let first = IndexPath(row: Int(max(contentOffset.y/rowHeight, 0)), section: 0)
        let last = IndexPath(row: min(Int((bounds.height + contentOffset.y.magnitude)/rowHeight), dataSource.numberOfRows() - 1), section: 0)
        visibleCells.forEach({ index, cell in
            if index < first || index > last {
                guard let cell = visibleCells.removeValue(forKey: index) else { return }
                reusableCells.insert(cell)
                cell.removeFromSuperview()
            }
        })
        for i in first.row...last.row where visibleCells[IndexPath(row: i, section: 0)] == nil {
            let indexPath = IndexPath(row: i, section: 0)
            let cell = dataSource.cellForRow(in: self, at: indexPath)
            self.addSubview(cell)
            visibleCells[indexPath] = cell
        }
    }
    
    func dequeueReusableCell(at indexPath: IndexPath) -> MyCell {
        guard let cell = visibleCells[indexPath] else {
            let cell: MyCell = !reusableCells.isEmpty ? reusableCells.removeFirst() : MyCell()
            cell.frame = .init(x: 0, y: rowHeight*CGFloat(indexPath.row), width: self.frame.width, height: rowHeight)
            return cell
        }
        return cell
    }
}

struct TestVR: UIViewControllerRepresentable {
    func makeUIViewController(context: Context) -> some UIViewController {
        return TestVC()
    }
    
    func updateUIViewController(_ uiViewController: UIViewControllerType, context: Context) {
        //
    }
}

struct DemoPresent: View {
    var body: some View {
        TestVR().ignoresSafeArea()
    }
}

#Preview {
    DemoPresent()
}

